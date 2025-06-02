<template>
  <div class="p-4 space-y-4">
    <!-- Filters -->
    <div class="flex flex-col w-full gap-4">
      <div class="flex w-full gap-4">
        <div class="flex flex-col w-1/2 gap-2">
          <label for="filterCardName" class="font-medium text-gray-700 text-theme-sm dark:text-gray-200">Card Name</label>
          <input
            id="filterCardName"
            v-model="filterCardName"
            type="text"
            placeholder="Filter by Card Name"
            class="w-full form-control"
          />
        </div>
        <div class="flex flex-col w-1/2 gap-2">
          <label for="filterUser" class="font-medium text-gray-700 text-theme-sm dark:text-gray-200">User</label>
          <input
            id="filterUser"
            v-model="filterUser"
            type="text"
            placeholder="Filter by User"
            class="w-full form-control"
          />
        </div>
      </div>
       <div class="flex w-full gap-4">
        <div class="flex flex-col w-1/2 gap-2">
           <label for="filterCardNumber" class="font-medium text-gray-700 text-theme-sm dark:text-gray-200">Card Number</label>
          <input
            id="filterCardNumber"
            v-model="filterCardNumber"
            type="text"
            placeholder="Filter by Card Number"
            class="w-full form-control"
          />
        </div>
       </div>
    </div>

    <!-- Table -->
    <div class="overflow-hidden rounded-xl border border-gray-200 bg-white dark:border-gray-800 dark:bg-white/[0.03]">
      <div class="max-w-full overflow-x-auto custom-scrollbar">
        <table class="min-w-full">
          <thead>
            <tr class="border-b border-gray-200 dark:border-gray-700">
              <th class="w-1/6 px-5 py-3 text-left sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Card Name</p>
              </th>
              <th class="w-1/6 px-5 py-3 text-left sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Card Number</p>
              </th>
              <th class="w-1/6 px-5 py-3 text-left sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">User</p>
              </th>
              <th class="w-1/6 px-5 py-3 text-left sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Last transaction</p>
              </th>
              <th class="w-1/6 px-5 py-3 text-left sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Actions</p>
              </th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-200 dark:divide-gray-700">
            <tr
              v-for="card in paginatedCards"
              :key="card.id"
              class="border-t border-gray-100 dark:border-gray-800"
            >
              <td class="flex items-center gap-2 px-5 py-4 sm:px-6">
                <span v-if="card.type === 'mastercard'">
                  <!-- Mastercard SVG -->
                  <svg width="32" height="24" viewBox="0 0 32 24" fill="none">
                    <rect width="32" height="24" rx="6" fill="#23272F"/>
                    <circle cx="13" cy="12" r="6" fill="#EB001B"/>
                    <circle cx="19" cy="12" r="6" fill="#F79E1B"/>
                    <circle cx="16" cy="12" r="6" fill="#FF5F00"/>
                  </svg>
                </span>
                <span v-else-if="card.type === 'visa'">
                  <!-- Visa SVG (add your SVG here) -->
                  <svg width="32" height="24" viewBox="0 0 32 24" fill="none">
                    <rect width="32" height="24" rx="6" fill="#23272F"/>
                  </svg>
                </span>
                <span class="ml-2 text-gray-500 text-theme-sm dark:text-gray-400">{{ card.name }}</span>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <span class="text-gray-500 text-theme-sm dark:text-gray-400">{{ card.number }}</span>
              </td>
              
              <td class="px-5 py-4 sm:px-6">
                <p class="text-gray-500 text-theme-sm dark:text-gray-400">@{{ card.user.toLowerCase().replace(/\s+/g, '') }}</p>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <p class="text-gray-500 text-theme-sm dark:text-gray-400">{{ card.lastTransaction }}</p>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <div class="flex items-center gap-2">
                  <button
                    class="px-3 py-1 text-sm text-white rounded-lg bg-brand-500 hover:bg-brand-600"
                    @click="viewCard(card)"
                  >
                    View
                  </button>
                  <button
                    class="px-3 py-1 text-sm text-gray-600 bg-gray-100 rounded-lg hover:bg-gray-200 dark:bg-gray-800 dark:text-gray-300 dark:hover:bg-gray-700"
                    @click="editCard(card)"
                  >
                    Edit
                  </button>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- Pagination -->
    <div class="flex items-center justify-between px-4 py-3 border-t border-gray-200 sm:px-6">
      <div class="flex justify-between flex-1 sm:hidden">
        <button
          @click="currentPage--"
          :disabled="currentPage === 1"
          class="relative inline-flex items-center px-4 py-2 text-sm font-medium text-gray-700 bg-white border border-gray-300 rounded-md hover:bg-gray-50"
        >
          Previous
        </button>
        <button
          @click="currentPage++"
          :disabled="currentPage === totalPages"
          class="relative inline-flex items-center px-4 py-2 ml-3 text-sm font-medium text-gray-700 bg-white border border-gray-300 rounded-md hover:bg-gray-50"
        >
          Next
        </button>
      </div>
      <div class="hidden sm:flex sm:flex-1 sm:items-center sm:justify-between">
        <div>
          <p class="text-sm text-gray-700">
            Showing
            <span class="font-medium">{{ startIndex + 1 }}</span>
            to
            <span class="font-medium">{{ endIndex }}</span>
            of
            <span class="font-medium">{{ filteredCards.length }}</span>
            results
          </p>
        </div>
        <div>
          <nav class="inline-flex -space-x-px rounded-md shadow-sm isolate" aria-label="Pagination">
            <button
              @click="currentPage--"
              :disabled="currentPage === 1"
              class="relative inline-flex items-center px-2 py-2 text-gray-400 rounded-l-md ring-1 ring-inset ring-gray-300 hover:bg-gray-50 focus:z-20 focus:outline-offset-0"
            >
              <span class="sr-only">Previous</span>
              <svg class="w-5 h-5" viewBox="0 0 20 20" fill="currentColor" aria-hidden="true">
                <path
                  fill-rule="evenodd"
                  d="M12.79 5.23a.75.75 0 01-.02 1.06L8.832 10l3.938 3.71a.75.75 0 11-1.04 1.08l-4.5-4.25a.75.75 0 010-1.08l4.5-4.25a.75.75 0 011.06.02z"
                  clip-rule="evenodd"
                />
              </svg>
            </button>
            <button
              v-for="page in totalPages"
              :key="page"
              @click="currentPage = page"
              :class="[
                'relative inline-flex items-center px-4 py-2 text-sm font-semibold',
                currentPage === page
                  ? 'z-10 bg-brand-500 text-white focus:z-20 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-brand-500'
                  : 'text-gray-900 ring-1 ring-inset ring-gray-300 hover:bg-gray-50 focus:z-20 focus:outline-offset-0',
              ]"
            >
              {{ page }}
            </button>
            <button
              @click="currentPage++"
              :disabled="currentPage === totalPages"
              class="relative inline-flex items-center px-2 py-2 text-gray-400 rounded-r-md ring-1 ring-inset ring-gray-300 hover:bg-gray-50 focus:z-20 focus:outline-offset-0"
            >
              <span class="sr-only">Next</span>
              <svg class="w-5 h-5" viewBox="0 0 20 20" fill="currentColor" aria-hidden="true">
                <path
                  fill-rule="evenodd"
                  d="M7.21 14.77a.75.75 0 01.02-1.06L11.168 10 7.23 6.29a.75.75 0 111.04-1.08l4.5 4.25a.75.75 0 010 1.08l-4.5 4.25a.75.75 0 01-1.06-.02z"
                  clip-rule="evenodd"
                />
              </svg>
            </button>
          </nav>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { ref, computed, defineComponent } from 'vue'

interface Card {
  id: string
  name: string
  number: string
  type: string
  status: string
  user: string
  lastTransaction: string
}

export default defineComponent({
  name: 'CardsTable',
  setup() {
    const cards = ref<Card[]>([
      {
        id: 'CARD001',
        name: '_Metal',
        number: '**7328',
        type: 'mastercard',
        status: 'Active',
        user: 'John Doe',
        lastTransaction: '2024-01-01'
      },
      {
        id: 'CARD002',
        name: '_Virtual',
        number: '**7377',
        type: 'mastercard',
        status: 'Inactive',
        user: 'Jane Smith',
        lastTransaction: '2024-01-01'
      }
    ])

    const filterCardName = ref('')
    const filterUser = ref('')
    const filterType = ref('')
    const filterStatus = ref('')
    const filterCardNumber = ref('')

    const uniqueTypes = computed(() => Array.from(new Set(cards.value.map((c: Card) => c.type))))
    const uniqueStatuses = computed(() => Array.from(new Set(cards.value.map((c: Card) => c.status))))

    const filteredCards = computed(() => {
      return cards.value.filter((c: Card) =>
        (!filterCardName.value || c.name.toLowerCase().includes(filterCardName.value.toLowerCase())) &&
        (!filterUser.value || c.user.toLowerCase().includes(filterUser.value.toLowerCase())) &&
        (!filterType.value || c.type === filterType.value) &&
        (!filterStatus.value || c.status === filterStatus.value) &&
        (!filterCardNumber.value || c.number.includes(filterCardNumber.value))
      )
    })

    // Pagination
    const currentPage = ref(1)
    const itemsPerPage = 10
    const totalPages = computed(() => Math.ceil(filteredCards.value.length / itemsPerPage))
    const startIndex = computed(() => (currentPage.value - 1) * itemsPerPage)
    const endIndex = computed(() => Math.min(startIndex.value + itemsPerPage, filteredCards.value.length))
    const paginatedCards = computed(() => filteredCards.value.slice(startIndex.value, endIndex.value))

    const viewCard = (card: Card): void => {
      console.log('View card:', card)
    }
    const editCard = (card: Card): void => {
      console.log('Edit card:', card)
    }

    return {
      cards,
      filterCardName,
      filterUser,
      filterType,
      filterStatus,
      filterCardNumber,
      uniqueTypes,
      uniqueStatuses,
      paginatedCards,
      filteredCards,
      currentPage,
      totalPages,
      startIndex,
      endIndex,
      viewCard,
      editCard
    }
  }
})
</script>

<style scoped>
.form-control {
  @apply w-full rounded-lg border border-gray-200 bg-white px-4 text-theme-sm text-gray-500 outline-none transition-all duration-200 ease-in-out focus:border-brand-500 focus:ring-0 dark:border-white/10 dark:bg-white/5 dark:text-gray-400;
}
</style> 