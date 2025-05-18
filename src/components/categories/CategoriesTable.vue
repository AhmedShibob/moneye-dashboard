<template>
  <div class="p-4 space-y-4">
    <!-- Header with Create Button -->
    <div class="flex items-center justify-end">
      <button
        class="btn btn-primary"
        @click="createCategory"
      >
        <span class="flex items-center gap-2">
          <svg
            class="w-5 h-5"
            viewBox="0 0 24 24"
            fill="none"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              d="M12 5V19M5 12H19"
              stroke="currentColor"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
          </svg>
          Create Category
        </span>
      </button>
    </div>

    <!-- Filters -->
    <div class="flex flex-col w-full gap-4">
      <div class="flex w-full gap-4">
        <div class="flex flex-col w-1/2 gap-2">
          <label for="filterCategoryName" class="font-medium text-gray-700 text-theme-sm dark:text-gray-200">Category Name</label>
          <input
            id="filterCategoryName"
            v-model="filterCategoryName"
            type="text"
            placeholder="Filter by Category Name"
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
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Icon</p>
              </th>
              <th class="w-1/6 px-5 py-3 text-left sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Category Name</p>
              </th>
              <th class="w-1/6 px-5 py-3 text-left sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Transactions</p>
              </th>
              <th class="w-1/6 px-5 py-3 text-left sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Status</p>
              </th>
              <th class="w-1/6 px-5 py-3 text-left sm:px-6">
                <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Actions</p>
              </th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-200 dark:divide-gray-700">
            <tr
              v-for="category in paginatedCategories"
              :key="category.id"
              class="border-t border-gray-100 dark:border-gray-800"
            >
              <td class="px-5 py-4 sm:px-6">
                <p class="text-gray-500 text-theme-sm dark:text-gray-400">{{ category.id }}</p>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <div class="flex items-center justify-center w-8 h-8">
                  <i :class="category.icon" class="text-xl"></i>
                </div>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <p class="text-gray-500 text-theme-sm dark:text-gray-400">{{ category.name }}</p>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <p class="text-gray-500 text-theme-sm dark:text-gray-400">{{ category.transactions }}</p>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <span
                  class="inline-flex items-center rounded-full px-2.5 py-0.5 text-xs font-medium"
                  :class="{
                    'bg-success-50 text-success-700 dark:bg-success-500/15 dark:text-success-500': category.status === 'Active',
                    'bg-error-50 text-error-700 dark:bg-error-500/15 dark:text-error-500': category.status === 'Inactive'
                  }"
                >
                  {{ category.status }}
                </span>
              </td>
              <td class="px-5 py-4 sm:px-6">
                <div class="flex items-center gap-2">
                  <button
                    class="px-3 py-1 text-sm text-white rounded-lg bg-brand-500 hover:bg-brand-600"
                    @click="viewCategory(category)"
                  >
                    View
                  </button>
                  <button
                    class="px-3 py-1 text-sm text-gray-600 bg-gray-100 rounded-lg hover:bg-gray-200 dark:bg-gray-800 dark:text-gray-300 dark:hover:bg-gray-700"
                    @click="editCategory(category)"
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
          <p class="text-sm text-gray-700 dark:text-gray-400">
            Showing
            <span class="font-medium">{{ startIndex + 1 }}</span>
            to
            <span class="font-medium">{{ endIndex }}</span>
            of
            <span class="font-medium">{{ filteredCategories.length }}</span>
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
</template>

<script lang="ts">
import { ref, computed } from 'vue'

interface Category {
  id: string
  icon: string
  name: string
  transactions: number
  status: string
}

export default {
  name: 'CategoriesTable',
  setup() {
    const categories = ref<Category[]>([
      {
        id: 'CAT001',
        icon: 'fas fa-shopping-cart',
        name: 'Shopping',
        transactions: 150,
        status: 'Active'
      },
      {
        id: 'CAT002',
        icon: 'fas fa-utensils',
        name: 'Food & Drink',
        transactions: 75,
        status: 'Active'
      },
      {
        id: 'CAT003',
        icon: 'fas fa-car',
        name: 'Transportation',
        transactions: 45,
        status: 'Active'
      },
      {
        id: 'CAT004',
        icon: 'fas fa-home',
        name: 'Housing',
        transactions: 30,
        status: 'Inactive'
      }
    ])

    const filterCategoryName = ref('')
    const filterStatus = ref('')

    const uniqueStatuses = computed(() => Array.from(new Set(categories.value.map(c => c.status))))

    const filteredCategories = computed(() => {
      return categories.value.filter(c =>
        (!filterCategoryName.value || c.name.toLowerCase().includes(filterCategoryName.value.toLowerCase())) &&
        (!filterStatus.value || c.status === filterStatus.value)
      )
    })

    // Pagination
    const currentPage = ref(1)
    const itemsPerPage = 10
    const totalPages = computed(() => Math.ceil(filteredCategories.value.length / itemsPerPage))
    const startIndex = computed(() => (currentPage.value - 1) * itemsPerPage)
    const endIndex = computed(() => Math.min(startIndex.value + itemsPerPage, filteredCategories.value.length))
    const paginatedCategories = computed(() => filteredCategories.value.slice(startIndex.value, endIndex.value))

    const viewCategory = (category: Category) => {
      console.log('View category:', category)
    }

    const editCategory = (category: Category) => {
      console.log('Edit category:', category)
    }

    const createCategory = () => {
      console.log('Create new category')
      // Implement create category logic
    }

    return {
      categories,
      filterCategoryName,
      filterStatus,
      uniqueStatuses,
      filteredCategories,
      currentPage,
      totalPages,
      startIndex,
      endIndex,
      paginatedCategories,
      viewCategory,
      editCategory,
      createCategory
    }
  }
}
</script>

<style scoped>
.form-control {
  @apply rounded-lg border border-gray-300 bg-transparent px-4 py-2.5 text-sm text-gray-800 shadow-theme-xs placeholder:text-gray-400 focus:border-brand-300 focus:outline-none focus:ring-2 focus:ring-brand-500/10 dark:border-gray-700 dark:bg-gray-900 dark:text-white/90 dark:placeholder:text-white/30 dark:focus:border-brand-800;
}

.btn {
  @apply inline-flex items-center justify-center rounded-lg px-4 py-2 text-theme-sm font-medium transition-all duration-200 ease-in-out;
}

.btn-primary {
  @apply bg-brand-500 text-white hover:bg-brand-600 focus:ring-2 focus:ring-brand-500/20;
}
</style> 