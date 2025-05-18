<template>
  <div class="space-y-4">
    <!-- Search and Filter -->
    <div class="flex flex-col w-full gap-4">
      <div class="flex w-full gap-4">
        <div class="flex flex-col w-1/2 gap-2">
          <label for="filterPhone" class="font-medium text-gray-700 text-theme-sm dark:text-gray-200">Phone Number</label>
          <input
            id="filterPhone"
            v-model="filterPhone"
            type="text"
            placeholder="Filter by Phone Number"
            class="w-full form-control"
          />
        </div>
        <div class="flex flex-col w-1/2 gap-2">
          <label for="filterStatus" class="font-medium text-gray-700 text-theme-sm dark:text-gray-200">Status</label>
          <select id="filterStatus" v-model="filterStatus" class="w-full form-control">
            <option value="">All Statuses</option>
            <option v-for="status in uniqueStatuses" :key="status" :value="status">{{ status }}</option>
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
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Phone Number</p>
              </th>
              <th class="w-1/6 px-5 py-3 text-left sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Message</p>
              </th>
              <th class="w-1/6 px-5 py-3 text-left sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Error</p>
              </th>
              <th class="w-1/6 px-5 py-3 text-left sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Date</p>
              </th>
              <th class="w-1/6 px-5 py-3 text-left sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Actions</p>
              </th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-200 dark:divide-gray-700">
            <tr
              v-for="sms in paginatedSMS"
              :key="sms.id"
              class="border-t border-gray-100 dark:border-gray-800"
            >
              <td class="px-5 py-4 sm:px-6">
                <p class="text-gray-500 text-theme-sm dark:text-gray-400">{{ sms.id }}</p>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <p class="text-gray-500 text-theme-sm dark:text-gray-400">{{ sms.phoneNumber }}</p>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <p class="text-gray-500 text-theme-sm dark:text-gray-400">{{ sms.message }}</p>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <span
                  class="inline-flex items-center rounded-full px-2.5 py-0.5 text-xs font-medium"
                  :class="getErrorClass(sms.error)"
                >
                  {{ sms.error }}
                </span>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <p class="text-gray-500 text-theme-sm dark:text-gray-400">{{ sms.date }}</p>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <div class="flex items-center gap-2">
                  <button
                    class="px-3 py-1 text-sm text-white rounded-lg bg-brand-500 hover:bg-brand-600"
                    @click="viewSMS(sms)"
                  >
                    View
                  </button>
                  <button
                    class="px-3 py-1 text-sm text-gray-600 bg-gray-100 rounded-lg hover:bg-gray-200 dark:bg-gray-800 dark:text-gray-300 dark:hover:bg-gray-700"
                    @click="retrySMS(sms)"
                  >
                    Retry
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
            <span class="font-medium">{{ failedSMS.length }}</span>
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
import { ref, computed } from 'vue'

interface FailedSMS {
  id: string
  phoneNumber: string
  message: string
  error: string
  date: string
}

export default {
  name: 'FailedSMSTable',
  setup() {
    const failedSMS = ref<FailedSMS[]>([
      {
        id: 'SMS001',
        phoneNumber: '+1 234 567 890',
        message: 'Your transaction of $99.99 at Amazon has been completed.',
        error: 'Invalid Number',
        date: '2024-03-15 14:30:00'
      },
      {
        id: 'SMS002',
        phoneNumber: '+1 234 567 891',
        message: 'Your transaction of $4.99 at Starbucks has been completed.',
        error: 'Network Error',
        date: '2024-03-15 14:35:00'
      },
      {
        id: 'SMS003',
        phoneNumber: '+1 234 567 892',
        message: 'Your transaction of $25.00 at Uber has been completed.',
        error: 'Service Unavailable',
        date: '2024-03-15 14:40:00'
      }
    ])

    const filterPhone = ref('')
    const filterStatus = ref('')

    const uniqueStatuses = computed(() => Array.from(new Set(failedSMS.value.map(s => s.error))))

    const filteredSMS = computed(() => {
      return failedSMS.value.filter(s =>
        (!filterPhone.value || s.phoneNumber.toLowerCase().includes(filterPhone.value.toLowerCase())) &&
        (!filterStatus.value || s.error === filterStatus.value)
      )
    })

    // Pagination
    const currentPage = ref(1)
    const itemsPerPage = 10
    const totalPages = computed(() => Math.ceil(filteredSMS.value.length / itemsPerPage))
    const startIndex = computed(() => (currentPage.value - 1) * itemsPerPage)
    const endIndex = computed(() => Math.min(startIndex.value + itemsPerPage, filteredSMS.value.length))
    const paginatedSMS = computed(() => filteredSMS.value.slice(startIndex.value, endIndex.value))

    const getErrorClass = (error: string) => {
      const classes = {
        'Invalid Number': 'bg-red-100 text-red-800 dark:bg-red-900 dark:text-red-300',
        'Network Error': 'bg-yellow-100 text-yellow-800 dark:bg-yellow-900 dark:text-yellow-300',
        'Service Unavailable': 'bg-orange-100 text-orange-800 dark:bg-orange-900 dark:text-orange-300'
      }
      return classes[error as keyof typeof classes] || 'bg-gray-100 text-gray-800 dark:bg-gray-700 dark:text-gray-300'
    }

    const viewSMS = (sms: FailedSMS) => {
      console.log('View SMS:', sms)
    }

    const retrySMS = (sms: FailedSMS) => {
      console.log('Retry SMS:', sms)
    }

    return {
      failedSMS,
      filterPhone,
      filterStatus,
      uniqueStatuses,
      currentPage,
      totalPages,
      startIndex,
      endIndex,
      paginatedSMS,
      getErrorClass,
      viewSMS,
      retrySMS
    }
  }
}
</script>

<style scoped>
.form-control {
  @apply w-full rounded-lg border border-gray-200 bg-white px-4 text-theme-sm text-gray-500 outline-none transition-all duration-200 ease-in-out focus:border-brand-500 focus:ring-0 dark:border-white/10 dark:bg-white/5 dark:text-gray-400;
}
</style> 