<template>
  <div class="p-4 space-y-4">
    <!-- Filters -->
    <div class="flex flex-col w-full gap-4">
      <div class="flex w-full gap-4">
        <div class="flex flex-col w-1/2 gap-2">
          <label for="filterMerchantName" class="font-medium text-gray-700 text-theme-sm dark:text-gray-200">Merchant Name</label>
          <input
            id="filterMerchantName"
            v-model="filterMerchantName"
            type="text"
            placeholder="Filter by Merchant Name"
            class="w-full form-control"
          />
        </div>
        <div class="flex flex-col w-1/2 gap-2">
          <label for="filterCategory" class="font-medium text-gray-700 text-theme-sm dark:text-gray-200">Category</label>
          <select id="filterCategory" v-model="filterCategory" class="w-full form-control">
            <option value="">All Categories</option>
            <option v-for="cat in uniqueCategories" :key="cat" :value="cat">{{ cat }}</option>
          </select>
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
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">ID</p>
              </th>
              <th class="w-1/6 px-5 py-3 text-left sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Merchant Name</p>
              </th>
              <th class="w-1/6 px-5 py-3 text-left sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Transaction Count</p>
              </th>
              <th class="w-1/6 px-5 py-3 text-left sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Category</p>
              </th>
              <th class="w-1/6 px-5 py-3 text-left sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Actions</p>
              </th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-200 dark:divide-gray-700">
            <tr
              v-for="merchant in paginatedMerchants"
              :key="merchant.id"
              class="border-t border-gray-100 dark:border-gray-800"
            >
              <td class="px-5 py-4 sm:px-6">
                <p class="text-gray-500 text-theme-sm dark:text-gray-400">{{ merchant.id }}</p>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <p class="text-gray-500 text-theme-sm dark:text-gray-400">{{ merchant.name }}</p>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <p class="text-gray-500 text-theme-sm dark:text-gray-400">{{ merchant.transactionCount }}</p>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <span
                  class="inline-flex items-center rounded-full px-2.5 py-0.5 text-xs font-medium"
                  :class="getCategoryClass(merchant.category)"
                >
                  {{ merchant.category }}
                </span>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <div class="flex items-center gap-2">
                  <button
                    class="px-3 py-1 text-sm text-white rounded-lg bg-brand-500 hover:bg-brand-600"
                    @click="viewMerchant(merchant)"
                  >
                    View
                  </button>
                  <button
                    class="px-3 py-1 text-sm text-gray-600 bg-gray-100 rounded-lg hover:bg-gray-200 dark:bg-gray-800 dark:text-gray-300 dark:hover:bg-gray-700"
                    @click="editMerchant(merchant)"
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
            <span class="font-medium">{{ merchants.length }}</span>
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

  <!-- Edit Merchant Modal -->
  <div v-if="showEditModal" class="fixed inset-0 z-50 overflow-y-auto">
    <div class="flex items-center justify-center min-h-screen px-4 pt-4 pb-20 text-center sm:block sm:p-0">
      <div class="fixed inset-0 transition-opacity" aria-hidden="true">
        <div class="absolute inset-0 bg-gray-500 opacity-75 dark:bg-gray-900"></div>
      </div>
      <div class="inline-block overflow-hidden text-left align-bottom transition-all transform bg-white rounded-lg shadow-xl dark:bg-gray-800 sm:my-8 sm:align-middle sm:max-w-lg sm:w-full">
        <div class="px-4 pt-5 pb-4 bg-white dark:bg-gray-800 sm:p-6 sm:pb-4">
          <div class="sm:flex sm:items-start">
            <div class="w-full mt-3 text-center sm:mt-0 sm:text-left">
              <h3 class="text-lg font-medium leading-6 text-gray-900 dark:text-gray-100">Edit Merchant</h3>
              <div class="mt-4 space-y-4">
                <div>
                  <label for="editMerchantName" class="block text-sm font-medium text-gray-700 dark:text-gray-200">Merchant Name</label>
                  <input
                    type="text"
                    id="editMerchantName"
                    v-model="editingMerchant.name"
                    class="w-full mt-1 form-control"
                  />
                </div>
                <div>
                  <label for="editCategory" class="block text-sm font-medium text-gray-700 dark:text-gray-200">Category</label>
                  <select
                    id="editCategory"
                    v-model="editingMerchant.category"
                    class="w-full mt-1 form-control"
                  >
                    <option v-for="cat in uniqueCategories" :key="cat" :value="cat">{{ cat }}</option>
                  </select>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="px-4 py-3 bg-gray-50 dark:bg-gray-700 sm:px-6 sm:flex sm:flex-row-reverse">
          <button
            type="button"
            class="inline-flex justify-center w-full px-4 py-2 text-base font-medium text-white border border-transparent rounded-md shadow-sm bg-brand-500 hover:bg-brand-600 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-brand-500 sm:ml-3 sm:w-auto sm:text-sm"
            @click="saveMerchant"
          >
            Save
          </button>
          <button
            type="button"
            class="inline-flex justify-center w-full px-4 py-2 mt-3 text-base font-medium text-gray-700 bg-white border border-gray-300 rounded-md shadow-sm hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-brand-500 sm:mt-0 sm:ml-3 sm:w-auto sm:text-sm dark:bg-gray-800 dark:text-gray-200 dark:border-gray-600 dark:hover:bg-gray-700"
            @click="showEditModal = false"
          >
            Cancel
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { ref, computed, defineComponent } from 'vue'

interface Merchant {
  id: string
  name: string
  transactionCount: number
  category: string
}

export default defineComponent({
  name: 'MerchantsTable',
  emits: ['update:merchants'],
  setup(_, { emit }) {
    const merchants = ref<Merchant[]>([
      {
        id: 'MCH001',
        name: 'Amazon',
        transactionCount: 150,
        category: 'Shopping'
      },
      {
        id: 'MCH002',
        name: 'Starbucks',
        transactionCount: 75,
        category: 'Food & Drink'
      },
      {
        id: 'MCH003',
        name: 'Uber',
        transactionCount: 45,
        category: 'Transportation'
      }
    ])

    const showEditModal = ref(false)
    const editingMerchant = ref<Merchant>({
      id: '',
      name: '',
      transactionCount: 0,
      category: ''
    })

    const filterMerchantName = ref('')
    const filterCategory = ref('')

    const uniqueCategories = computed(() => Array.from(new Set(merchants.value.map(m => m.category))))

    const filteredMerchants = computed(() => {
      return merchants.value.filter(m =>
        (!filterMerchantName.value || m.name.toLowerCase().includes(filterMerchantName.value.toLowerCase())) &&
        (!filterCategory.value || m.category === filterCategory.value)
      )
    })

    // Pagination
    const currentPage = ref(1)
    const itemsPerPage = 10
    const totalPages = computed(() => Math.ceil(filteredMerchants.value.length / itemsPerPage))
    const startIndex = computed(() => (currentPage.value - 1) * itemsPerPage)
    const endIndex = computed(() => Math.min(startIndex.value + itemsPerPage, filteredMerchants.value.length))
    const paginatedMerchants = computed(() => filteredMerchants.value.slice(startIndex.value, endIndex.value))

    const getCategoryClass = (category: string) => {
      const classes: Record<string, string> = {
        'Shopping': 'bg-blue-100 text-blue-800 dark:bg-blue-900 dark:text-blue-300',
        'Food & Drink': 'bg-green-100 text-green-800 dark:bg-green-900 dark:text-green-300',
        'Transportation': 'bg-yellow-100 text-yellow-800 dark:bg-yellow-900 dark:text-yellow-300',
        'Entertainment': 'bg-purple-100 text-purple-800 dark:bg-purple-900 dark:text-purple-300',
        'Utilities': 'bg-red-100 text-red-800 dark:bg-red-900 dark:text-red-300',
      }
      return classes[category] || 'bg-gray-100 text-gray-800 dark:bg-gray-700 dark:text-gray-300'
    }

    const viewMerchant = (merchant: Merchant): void => {
      console.log('View merchant:', merchant)
    }

    const editMerchant = (merchant: Merchant): void => {
      editingMerchant.value = { ...merchant }
      showEditModal.value = true
    }

    const saveMerchant = (): void => {
      const index = merchants.value.findIndex(m => m.id === editingMerchant.value.id)
      if (index !== -1) {
        merchants.value[index] = { ...editingMerchant.value }
        emit('update:merchants', merchants.value)
      }
      showEditModal.value = false
    }

    return {
      merchants,
      currentPage,
      totalPages,
      startIndex,
      endIndex,
      paginatedMerchants,
      getCategoryClass,
      viewMerchant,
      editMerchant,
      filterMerchantName,
      filterCategory,
      uniqueCategories,
      showEditModal,
      editingMerchant,
      saveMerchant
    }
  }
})
</script>

<style scoped>
.form-control {
  @apply w-full rounded-lg border border-gray-200 bg-white px-4 text-theme-sm text-gray-500 outline-none transition-all duration-200 ease-in-out focus:border-brand-500 focus:ring-0 dark:border-white/10 dark:bg-white/5 dark:text-gray-400;
}
</style> 