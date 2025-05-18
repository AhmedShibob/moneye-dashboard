<template>
  <div class="space-y-4">
    <!-- Search and Filter -->

    <div class="mb-6">
      <UserFilter
        @search="handleSearch"
        @filter="handleFilter"
        @sort="handleSort"
      />
    </div>
    
    <!-- Table -->
    <div
      class="overflow-hidden rounded-xl border border-gray-200 bg-white dark:border-gray-800 dark:bg-white/[0.03]"
    >
      <div class="max-w-full overflow-x-auto custom-scrollbar">
        <table class="min-w-full">
          <thead>
            <tr class="border-b border-gray-200 dark:border-gray-700">
              <th class="px-5 py-3 text-left w-1/6 sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">User ID</p>
              </th>
              <th class="px-5 py-3 text-left w-1/6 sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Phone</p>
              </th>
              <th class="px-5 py-3 text-left w-1/6 sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Transactions</p>
              </th>
              <th class="px-5 py-3 text-left w-1/6 sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Cards</p>
              </th>
              <th class="px-5 py-3 text-left w-1/6 sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Join Date</p>
              </th>
              <th class="px-5 py-3 text-left w-1/6 sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Actions</p>
              </th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-200 dark:divide-gray-700">
            <tr
              v-for="user in paginatedUsers"
              :key="user.id"
              class="border-t border-gray-100 dark:border-gray-800"
            >
              <td class="px-5 py-4 sm:px-6">
                <p class="text-gray-500 text-theme-sm dark:text-gray-400">{{ user.id }}</p>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <p class="text-gray-500 text-theme-sm dark:text-gray-400">{{ user.phone }}</p>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <p class="text-gray-500 text-theme-sm dark:text-gray-400">{{ user.transactions }}</p>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <p class="text-gray-500 text-theme-sm dark:text-gray-400">{{ user.cards }}</p>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <p class="text-gray-500 text-theme-sm dark:text-gray-400">{{ user.joinDate }}</p>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <div class="flex items-center gap-2">
                  <button
                    class="rounded-lg bg-brand-500 px-3 py-1 text-sm text-white hover:bg-brand-600"
                    @click="viewUser(user)"
                  >
                    View
                  </button>
                  <button
                    class="rounded-lg bg-gray-100 px-3 py-1 text-sm text-gray-600 hover:bg-gray-200 dark:bg-gray-800 dark:text-gray-300 dark:hover:bg-gray-700"
                    @click="editUser(user)"
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
    <div class="flex items-center justify-between border-t border-gray-200 px-4 py-3 sm:px-6">
      <div class="flex flex-1 justify-between sm:hidden">
        <button
          @click="currentPage--"
          :disabled="currentPage === 1"
          class="relative inline-flex items-center rounded-md border border-gray-300 bg-white px-4 py-2 text-sm font-medium text-gray-700 hover:bg-gray-50"
        >
          Previous
        </button>
        <button
          @click="currentPage++"
          :disabled="currentPage === totalPages"
          class="relative ml-3 inline-flex items-center rounded-md border border-gray-300 bg-white px-4 py-2 text-sm font-medium text-gray-700 hover:bg-gray-50"
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
            <span class="font-medium">{{ filteredUsers.length }}</span>
            results
          </p>
        </div>
        <div>
          <nav class="isolate inline-flex -space-x-px rounded-md shadow-sm" aria-label="Pagination">
            <button
              @click="currentPage--"
              :disabled="currentPage === 1"
              class="relative inline-flex items-center rounded-l-md px-2 py-2 text-gray-400 ring-1 ring-inset ring-gray-300 hover:bg-gray-50 focus:z-20 focus:outline-offset-0"
            >
              <span class="sr-only">Previous</span>
              <svg class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor" aria-hidden="true">
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
              class="relative inline-flex items-center rounded-r-md px-2 py-2 text-gray-400 ring-1 ring-inset ring-gray-300 hover:bg-gray-50 focus:z-20 focus:outline-offset-0"
            >
              <span class="sr-only">Next</span>
              <svg class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor" aria-hidden="true">
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

<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue'
import type { User } from '@/types/user'
import flatpickr from 'flatpickr'
import 'flatpickr/dist/flatpickr.css'
import UserFilter from '../UserFilter.vue'

// Sample data - replace with actual API call
const users = ref<User[]>([
  {
    id: 'USR001',
    phone: '+1 234 567 890',
    transactions: 15,
    cards: 2,
    joinDate: '2024-01-15',
  },
  {
    id: 'USR002',
    phone: '+1 234 567 891',
    transactions: 8,
    cards: 1,
    joinDate: '2024-02-01',
  },
  {
    id: 'USR003',
    phone: '+1 234 567 892',
    transactions: 23,
    cards: 3,
    joinDate: '2024-02-15',
  },
  // Add more sample data as needed
])

const searchQuery = ref('')
const selectedRole = ref('')
const selectedStatus = ref('')
const sortBy = ref('')
const transactionFilter = ref('')
const startDate = ref('')
const endDate = ref('')
const currentPage = ref(1)
const itemsPerPage = 10

// Datepicker refs
const startDateInput = ref<HTMLInputElement | null>(null)
const endDateInput = ref<HTMLInputElement | null>(null)
let startDatePicker: any = null
let endDatePicker: any = null

// Event handlers
const handleSearch = (query: string) => {
  searchQuery.value = query
}

const handleFilter = (filters: { role: string; status: string }) => {
  selectedRole.value = filters.role
  selectedStatus.value = filters.status
}

const handleSort = (sort: string) => {
  sortBy.value = sort
}

// Initialize datepickers
onMounted(() => {
  if (startDateInput.value) {
    startDatePicker = flatpickr(startDateInput.value, {
      dateFormat: 'Y-m-d',
      onChange: (selectedDates) => {
        startDate.value = selectedDates[0] ? selectedDates[0].toISOString().split('T')[0] : ''
        if (endDatePicker) {
          endDatePicker.set('minDate', selectedDates[0])
        }
      },
    })
  }

  if (endDateInput.value) {
    endDatePicker = flatpickr(endDateInput.value, {
      dateFormat: 'Y-m-d',
      onChange: (selectedDates) => {
        endDate.value = selectedDates[0] ? selectedDates[0].toISOString().split('T')[0] : ''
        if (startDatePicker) {
          startDatePicker.set('maxDate', selectedDates[0])
        }
      },
    })
  }
})

// Cleanup datepickers
onUnmounted(() => {
  if (startDatePicker) {
    startDatePicker.destroy()
  }
  if (endDatePicker) {
    endDatePicker.destroy()
  }
})

// Filtered users based on search and filters
const filteredUsers = computed(() => {
  return users.value.filter((user: User) => {
    // Search filter
    const matchesSearch =
      user.id.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
      user.phone.toLowerCase().includes(searchQuery.value.toLowerCase())

    // Transaction count filter
    let matchesTransaction = true
    if (transactionFilter.value) {
      const [min, max] = transactionFilter.value.split('-').map(Number)
      if (max) {
        matchesTransaction = user.transactions >= min && user.transactions <= max
      } else {
        matchesTransaction = user.transactions >= min
      }
    }

    // Date range filter
    let matchesDate = true
    if (startDate.value && endDate.value) {
      const userDate = new Date(user.joinDate)
      const start = new Date(startDate.value)
      const end = new Date(endDate.value)
      matchesDate = userDate >= start && userDate <= end
    }

    return matchesSearch && matchesTransaction && matchesDate
  })
})

// Pagination
const totalPages = computed(() => Math.ceil(filteredUsers.value.length / itemsPerPage))
const startIndex = computed(() => (currentPage.value - 1) * itemsPerPage)
const endIndex = computed(() => Math.min(startIndex.value + itemsPerPage, filteredUsers.value.length))
const paginatedUsers = computed(() => {
  return filteredUsers.value.slice(startIndex.value, endIndex.value)
})

// Actions
const viewUser = (user: User) => {
  console.log('View user:', user)
  // Implement view logic
}

const editUser = (user: User) => {
  console.log('Edit user:', user)
  // Implement edit logic
}
</script>

<style>
/* Add flatpickr custom styles */
.flatpickr-calendar {
  @apply bg-white dark:bg-gray-800 border border-gray-200 dark:border-gray-700 rounded-lg shadow-lg;
}

.flatpickr-day {
  @apply text-gray-700 dark:text-gray-300 hover:bg-gray-100 dark:hover:bg-gray-700;
}

.flatpickr-day.selected {
  @apply bg-brand-500 text-white hover:bg-brand-600;
}

.flatpickr-day.today {
  @apply border-brand-500;
}

.flatpickr-months .flatpickr-month {
  @apply bg-white dark:bg-gray-800 text-gray-900 dark:text-white;
}

.flatpickr-current-month .flatpickr-monthDropdown-months {
  @apply text-gray-900 dark:text-white;
}

.flatpickr-weekday {
  @apply text-gray-500 dark:text-gray-400;
}
</style>