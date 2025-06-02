<template>
  <div class="p-4 space-y-4">
    
        <div class="flex flex-col w-full gap-4 ">
          <div class="flex w-full gap-4">
            <div class="flex flex-col w-1/2 gap-2">
              <label for="filterUserId" class="font-medium text-gray-700 text-theme-sm dark:text-gray-200">User ID</label>
              <input
                id="filterUserId"
                v-model="filterUserId"
                type="text"
                placeholder="Filter by User ID"
                class="w-full form-control"
              />
            </div>
            <div class="flex flex-col w-1/2 gap-2">
              <label for="filterMerchant" class="font-medium text-gray-700 text-theme-sm dark:text-gray-200">Merchant</label>
              <select id="filterMerchant" v-model="filterMerchant" class="w-full form-control">
                <option value="">All Merchants</option>
                <option v-for="merchant in uniqueMerchants" :key="merchant" :value="merchant">{{ merchant }}</option>
              </select>
            </div>
          </div>
          <div class="flex gap-4">
            <div class="flex flex-col w-1/2 gap-2">
              <label for="filterCard" class="font-medium text-gray-700 text-theme-sm dark:text-gray-200">Card</label>
              <input
                id="filterCard"
                v-model="filterCard"
                type="text"
                placeholder="Filter by Card"
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
    <div
      class="overflow-hidden rounded-xl border border-gray-200 bg-white dark:border-gray-800 dark:bg-white/[0.03]"
    >
      <div class="max-w-full overflow-x-auto custom-scrollbar">
        <table class="min-w-full">
          <thead>
            <tr class="border-b border-gray-200 dark:border-gray-700">
            
              <th class="px-5 py-3 text-left w-1/8 sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">User</p>
              </th>
               <th class="px-5 py-3 text-left w-1/8 sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Date</p>
              </th>
               <th class="px-5 py-3 text-left w-1/8 sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Card</p>
              </th>
              <th class="px-5 py-3 text-left w-1/8 sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Merchant</p>
              </th>
             
              <th class="px-5 py-3 text-left w-1/8 sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Amount</p>
              </th>
              <th class="px-5 py-3 text-left w-1/8 sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Category</p>
              </th>
              <th class="px-5 py-3 text-left w-1/8 sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Status</p>
              </th>
             
              <th class="px-5 py-3 text-left w-1/8 sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Actions</p>
              </th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-200 dark:divide-gray-700">
            <tr
              v-for="transaction in paginatedTransactions"
              :key="transaction.id"
              class="border-t border-gray-100 dark:border-gray-800"
            >
              
              <td class="px-5 py-4 sm:px-6">
                <p class="text-gray-500 text-theme-sm dark:text-gray-400">@{{ transaction.user.toLowerCase().replace(/\s+/g, '') }}</p>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <p class="text-gray-500 text-theme-sm dark:text-gray-400">{{ transaction.date }}</p>
              </td>
               <td class="flex items-center gap-2 px-5 py-4 sm:px-6">
              
                <span class="ml-2 text-gray-500 text-theme-sm dark:text-gray-400">{{ transaction.cardName }}</span>
                <span class="ml-2 text-gray-500 text-theme-sm dark:text-gray-400">{{ transaction.card }}</span>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <p class="text-gray-500 text-theme-sm dark:text-gray-400">{{ transaction.merchant }}</p>
              </td>
             
              <td class="px-5 py-4 sm:px-6">
                <p class="text-gray-500 text-theme-sm dark:text-gray-400">${{ transaction.amount }}</p>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <span
                  class="inline-flex items-center rounded-full px-2.5 py-0.5 text-xs font-medium"
                  :class="getCategoryClass(transaction.category)"
                >
                  {{ transaction.category }}
                </span>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <span
                  class="inline-flex items-center rounded-full px-2.5 py-0.5 text-xs font-medium"
                  :class="getStatusClass(transaction.status)"
                >
                  {{ transaction.status }}
                </span>
              </td>
              
              <td class="px-5 py-4 sm:px-6">
                <div class="flex items-center gap-2">
                  <button
                    class="px-3 py-1 text-sm text-white rounded-lg bg-brand-500 hover:bg-brand-600"
                    @click="viewTransaction(transaction)"
                  >
                    View
                  </button>
                  <button
                    class="px-3 py-1 text-sm text-gray-600 bg-gray-100 rounded-lg hover:bg-gray-200 dark:bg-gray-800 dark:text-gray-300 dark:hover:bg-gray-700"
                    @click="editTransaction(transaction)"
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
            <span class="font-medium">{{ transactions.length }}</span>
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

interface Transaction {
  id: string
  user: string
  merchant: string
  card: string
  cardType: string
  cardName: string
  amount: number
  category: string
  date: string
  status: string
}

export default {
  name: 'TransactionsTable',
  setup() {
    const transactions = ref<Transaction[]>([
      {
        id: 'TRX001',
        user: 'John Doe',
        merchant: 'Amazon',
        card: '**** 1234',
        cardType: 'mastercard',
        cardName: '_Metal',
        amount: 99.99,
        category: 'Shopping',
        date: '2024-03-15',
        status: 'Completed'
      },
      {
        id: 'TRX002',
        user: 'Jane Smith',
        merchant: 'Starbucks',
        card: '**** 5678',
        cardType: 'mastercard',
        cardName: '_Metal',
        amount: 4.99,
        category: 'Food & Drink',
        date: '2024-03-14',
        status: 'Pending'
      },
    ])

    const currentPage = ref(1)
    const itemsPerPage = 10

    const filterUserId = ref('');
    const filterMerchant = ref('');
    const filterCard = ref('');
    const filterCategory = ref('');

    const uniqueCategories = computed(() => {
      return Array.from(new Set(transactions.value.map(t => t.category)));
    });

    const uniqueMerchants = computed(() => {
      return Array.from(new Set(transactions.value.map(t => t.merchant)));
    });

    const filteredTransactions = computed(() => {
      return transactions.value.filter(t =>
        (!filterUserId.value || t.user.toLowerCase().includes(filterUserId.value.toLowerCase())) &&
        (!filterMerchant.value || t.merchant.toLowerCase().includes(filterMerchant.value.toLowerCase())) &&
        (!filterCard.value || t.card.toLowerCase().includes(filterCard.value.toLowerCase())) &&
        (!filterCategory.value || t.category === filterCategory.value)
      );
    });

    const totalPages = computed(() => Math.ceil(filteredTransactions.value.length / itemsPerPage))
    const startIndex = computed(() => (currentPage.value - 1) * itemsPerPage)
    const endIndex = computed(() => Math.min(startIndex.value + itemsPerPage, filteredTransactions.value.length))
    const paginatedTransactions = computed(() => {
      return filteredTransactions.value.slice(startIndex.value, endIndex.value)
    })

    const getCategoryClass = (category: string) => {
      const classes = {
        'Shopping': 'bg-blue-100 text-blue-800 dark:bg-blue-900 dark:text-blue-300',
        'Food & Drink': 'bg-green-100 text-green-800 dark:bg-green-900 dark:text-green-300',
        'Transportation': 'bg-yellow-100 text-yellow-800 dark:bg-yellow-900 dark:text-yellow-300',
        'Entertainment': 'bg-purple-100 text-purple-800 dark:bg-purple-900 dark:text-purple-300',
        'Utilities': 'bg-red-100 text-red-800 dark:bg-red-900 dark:text-red-300',
      }
      return classes[category as keyof typeof classes] || 'bg-gray-100 text-gray-800 dark:bg-gray-700 dark:text-gray-300'
    }

    const getStatusClass = (status: string) => {
      const classes = {
        'Completed': 'bg-green-100 text-green-800 dark:bg-green-900 dark:text-green-300',
        'Pending': 'bg-yellow-100 text-yellow-800 dark:bg-yellow-900 dark:text-yellow-300',
        'Failed': 'bg-red-100 text-red-800 dark:bg-red-900 dark:text-red-300',
        'Refunded': 'bg-blue-100 text-blue-800 dark:bg-blue-900 dark:text-blue-300',
      }
      return classes[status as keyof typeof classes] || 'bg-gray-100 text-gray-800 dark:bg-gray-700 dark:text-gray-300'
    }

    const viewTransaction = (transaction: Transaction) => {
      console.log('View transaction:', transaction)
    }

    const editTransaction = (transaction: Transaction) => {
      console.log('Edit transaction:', transaction)
    }

    return {
      transactions,
      currentPage,
      totalPages,
      startIndex,
      endIndex,
      paginatedTransactions,
      getCategoryClass,
      getStatusClass,
      viewTransaction,
      editTransaction,
      filterUserId,
      filterMerchant,
      filterCard,
      filterCategory,
      uniqueCategories,
      uniqueMerchants
    }
  }
}
</script> 

<style scoped>
.form-control {
  @apply w-full rounded-lg border border-gray-200 bg-white px-4 text-theme-sm text-gray-500 outline-none transition-all duration-200 ease-in-out focus:border-brand-500 focus:ring-0 dark:border-white/10 dark:bg-white/5 dark:text-gray-400;
}
</style> 