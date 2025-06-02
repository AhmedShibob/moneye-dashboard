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
                  <img 
                    v-if="category.icon.startsWith('data:')" 
                    :src="category.icon" 
                    class="object-contain w-6 h-6"
                    alt="Category icon"
                  />
                  <span 
                    v-else-if="category.icon.match(/[\u{1F300}-\u{1F9FF}]/u)" 
                    class="text-xl"
                  >
                    {{ category.icon }}
                  </span>
                  <i v-else :class="category.icon" class="text-xl"></i>
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

  <!-- Create Category Modal -->
  <div v-if="showCreateModal" class="fixed inset-0 z-50 overflow-y-auto top-32">
    <div class="flex items-center justify-center min-h-screen px-4 pt-4 pb-20 text-center sm:block sm:p-0">
      <div class="fixed inset-0 transition-opacity" aria-hidden="true">
        <div class="absolute inset-0 bg-gray-500 opacity-75 dark:bg-gray-900"></div>
      </div>
      <div class="inline-block overflow-hidden text-left align-bottom transition-all transform bg-white rounded-lg shadow-xl dark:bg-gray-800 sm:my-8 sm:align-middle sm:max-w-lg sm:w-full">
        <div class="px-4 pt-5 pb-4 bg-white dark:bg-gray-800 sm:p-6 sm:pb-4">
          <div class="sm:flex sm:items-start">
            <div class="w-full mt-3 text-center sm:mt-0 sm:text-left">
              <h3 class="text-lg font-medium leading-6 text-gray-900 dark:text-gray-100">Create New Category</h3>
              <div class="mt-4 space-y-4">
                <div>
                  <label for="categoryName" class="block text-sm font-medium text-gray-700 dark:text-gray-200">Category Name</label>
                  <input
                    type="text"
                    id="categoryName"
                    v-model="newCategory.name"
                    class="w-full mt-1 form-control"
                    placeholder="Enter category name"
                  />
                </div>
                <div>
                  <label class="block text-sm font-medium text-gray-700 dark:text-gray-200">Category Icon</label>
                  <div class="flex flex-col items-center gap-4 mt-2">
                    <div 
                      class="flex items-center justify-center w-24 h-24 border-2 border-gray-300 border-dashed rounded-lg cursor-pointer dark:border-gray-600 hover:border-brand-500 dark:hover:border-brand-500"
                      @click="triggerIconUpload"
                    >
                      <div v-if="!newCategory.icon || newCategory.icon.startsWith('fas fa-')" class="text-center">
                        <i v-if="newCategory.icon" :class="newCategory.icon" class="text-4xl text-gray-400"></i>
                        <svg v-else class="w-10 h-10 mx-auto text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6v6m0 0v6m0-6h6m-6 0H6" />
                        </svg>
                        <span class="mt-1 text-xs text-gray-500 dark:text-gray-400">Upload Icon</span>
                      </div>
                      <img 
                        v-else-if="newCategory.icon.startsWith('data:')" 
                        :src="newCategory.icon" 
                        class="object-contain w-20 h-20"
                        alt="Category icon"
                      />
                      <span 
                        v-else 
                        class="text-4xl"
                      >
                        {{ newCategory.icon }}
                      </span>
                    </div>
                    <input
                      type="file"
                      ref="iconInput"
                      class="hidden"
                      accept="image/*"
                      @change="handleIconUpload"
                    />
                    <button
                      v-if="newCategory.icon"
                      @click="removeIcon"
                      class="text-sm text-red-500 hover:text-red-700"
                    >
                      Remove Icon
                    </button>
                  </div>
                </div>
                <div>
                  <label class="block mb-2 text-sm font-medium text-gray-700 dark:text-gray-200">Or Choose a Predefined Icon</label>
                  <div class="grid grid-cols-6 gap-2 p-2 border border-gray-200 rounded-lg dark:border-gray-700">
                    <button
                      v-for="emoji in predefinedIcons"
                      :key="emoji"
                      @click="selectPredefinedIcon(emoji)"
                      class="flex items-center justify-center w-10 h-10 text-2xl transition-colors rounded-lg hover:bg-gray-100 dark:hover:bg-gray-700"
                      :class="{ 'bg-brand-50 dark:bg-brand-900/20': newCategory.icon === emoji }"
                    >
                      {{ emoji }}
                    </button>
                  </div>
                </div>
                <div>
                  <label for="categoryStatus" class="block text-sm font-medium text-gray-700 dark:text-gray-200">Status</label>
                  <select
                    id="categoryStatus"
                    v-model="newCategory.status"
                    class="w-full mt-1 form-control"
                  >
                    <option value="Active">Active</option>
                    <option value="Inactive">Inactive</option>
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
            @click="saveCategory"
            :disabled="!newCategory.name"
          >
            Create
          </button>
          <button
            type="button"
            class="inline-flex justify-center w-full px-4 py-2 mt-3 text-base font-medium text-gray-700 bg-white border border-gray-300 rounded-md shadow-sm hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-brand-500 sm:mt-0 sm:ml-3 sm:w-auto sm:text-sm dark:bg-gray-800 dark:text-gray-200 dark:border-gray-600 dark:hover:bg-gray-700"
            @click="closeCreateModal"
          >
            Cancel
          </button>
        </div>
      </div>
    </div>
  </div>

  <!-- Edit Category Modal -->
  <div v-if="showEditModal" class="fixed inset-0 z-50 overflow-y-auto top-32">
    <div class="flex items-center justify-center min-h-screen px-4 pt-4 pb-20 text-center sm:block sm:p-0">
      <div class="fixed inset-0 transition-opacity" aria-hidden="true">
        <div class="absolute inset-0 bg-gray-500 opacity-75 dark:bg-gray-900"></div>
      </div>
      <div class="inline-block overflow-hidden text-left align-bottom transition-all transform bg-white rounded-lg shadow-xl dark:bg-gray-800 sm:my-8 sm:align-middle sm:max-w-lg sm:w-full">
        <div class="px-4 pt-5 pb-4 bg-white dark:bg-gray-800 sm:p-6 sm:pb-4">
          <div class="sm:flex sm:items-start">
            <div class="w-full mt-3 text-center sm:mt-0 sm:text-left">
              <h3 class="text-lg font-medium leading-6 text-gray-900 dark:text-gray-100">Edit Category</h3>
              <div class="mt-4 space-y-4">
                <div>
                  <label for="editCategoryName" class="block text-sm font-medium text-gray-700 dark:text-gray-200">Category Name</label>
                  <input
                    type="text"
                    id="editCategoryName"
                    v-model="editingCategory.name"
                    class="w-full mt-1 form-control"
                    placeholder="Enter category name"
                  />
                </div>
                <div>
                  <label class="block text-sm font-medium text-gray-700 dark:text-gray-200">Category Icon</label>
                  <div class="flex flex-col items-center gap-4 mt-2">
                    <div 
                      class="flex items-center justify-center w-24 h-24 border-2 border-gray-300 border-dashed rounded-lg cursor-pointer dark:border-gray-600 hover:border-brand-500 dark:hover:border-brand-500"
                      @click="triggerEditIconUpload"
                    >
                      <div v-if="!editingCategory.icon || editingCategory.icon.startsWith('fas fa-')" class="text-center">
                        <i v-if="editingCategory.icon" :class="editingCategory.icon" class="text-4xl text-gray-400"></i>
                        <svg v-else class="w-10 h-10 mx-auto text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6v6m0 0v6m0-6h6m-6 0H6" />
                        </svg>
                        <span class="mt-1 text-xs text-gray-500 dark:text-gray-400">Upload Icon</span>
                      </div>
                      <img 
                        v-else-if="editingCategory.icon.startsWith('data:')" 
                        :src="editingCategory.icon" 
                        class="object-contain w-20 h-20"
                        alt="Category icon"
                      />
                      <span 
                        v-else 
                        class="text-4xl"
                      >
                        {{ editingCategory.icon }}
                      </span>
                    </div>
                    <input
                      type="file"
                      ref="editIconInput"
                      class="hidden"
                      accept="image/*"
                      @change="handleEditIconUpload"
                    />
                    <button
                      v-if="editingCategory.icon"
                      @click="removeEditIcon"
                      class="text-sm text-red-500 hover:text-red-700"
                    >
                      Remove Icon
                    </button>
                  </div>
                </div>

                <!-- Predefined Icons -->
                <div>
                  <label class="block mb-2 text-sm font-medium text-gray-700 dark:text-gray-200">Or Choose a Predefined Icon</label>
                  <div class="grid grid-cols-6 gap-2 p-2 border border-gray-200 rounded-lg dark:border-gray-700">
                    <button
                      v-for="emoji in predefinedIcons"
                      :key="emoji"
                      @click="selectEditPredefinedIcon(emoji)"
                      class="flex items-center justify-center w-10 h-10 text-2xl transition-colors rounded-lg hover:bg-gray-100 dark:hover:bg-gray-700"
                      :class="{ 'bg-brand-50 dark:bg-brand-900/20': editingCategory.icon === emoji }"
                    >
                      {{ emoji }}
                    </button>
                  </div>
                </div>

                <div>
                  <label for="editCategoryStatus" class="block text-sm font-medium text-gray-700 dark:text-gray-200">Status</label>
                  <select
                    id="editCategoryStatus"
                    v-model="editingCategory.status"
                    class="w-full mt-1 form-control"
                  >
                    <option value="Active">Active</option>
                    <option value="Inactive">Inactive</option>
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
            @click="updateCategory"
            :disabled="!editingCategory.name"
          >
            Save Changes
          </button>
          <button
            type="button"
            class="inline-flex justify-center w-full px-4 py-2 mt-3 text-base font-medium text-gray-700 bg-white border border-gray-300 rounded-md shadow-sm hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-brand-500 sm:mt-0 sm:ml-3 sm:w-auto sm:text-sm dark:bg-gray-800 dark:text-gray-200 dark:border-gray-600 dark:hover:bg-gray-700"
            @click="closeEditModal"
          >
            Cancel
          </button>
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

    const showEditModal = ref(false)
    const editIconInput = ref<HTMLInputElement | null>(null)
    const editingCategory = ref<Category>({
      id: '',
      icon: '',
      name: '',
      transactions: 0,
      status: 'Active'
    })

    const editCategory = (category: Category) => {
      editingCategory.value = { ...category }
      showEditModal.value = true
    }

    const closeEditModal = () => {
      showEditModal.value = false
      editingCategory.value = {
        id: '',
        icon: '',
        name: '',
        transactions: 0,
        status: 'Active'
      }
    }

    const triggerEditIconUpload = () => {
      editIconInput.value?.click()
    }

    const handleEditIconUpload = (event: Event) => {
      const target = event.target as HTMLInputElement
      const file = target.files?.[0]
      if (file) {
        const reader = new FileReader()
        reader.onload = (e) => {
          editingCategory.value.icon = e.target?.result as string
        }
        reader.readAsDataURL(file)
      }
    }

    const removeEditIcon = () => {
      editingCategory.value.icon = 'fas fa-folder'
      if (editIconInput.value) {
        editIconInput.value.value = ''
      }
    }

    const selectEditPredefinedIcon = (emoji: string) => {
      editingCategory.value.icon = emoji
    }

    const updateCategory = () => {
      if (!editingCategory.value.name) return

      const index = categories.value.findIndex(c => c.id === editingCategory.value.id)
      if (index !== -1) {
        categories.value[index] = { ...editingCategory.value }
      }
      closeEditModal()
    }

    const showCreateModal = ref(false)
    const iconInput = ref<HTMLInputElement | null>(null)
    const newCategory = ref<Category>({
      id: '',
      icon: '',
      name: '',
      transactions: 0,
      status: 'Active'
    })

    const createCategory = () => {
      showCreateModal.value = true
    }

    const closeCreateModal = () => {
      showCreateModal.value = false
      newCategory.value = {
        id: '',
        icon: '',
        name: '',
        transactions: 0,
        status: 'Active'
      }
    }

    const triggerIconUpload = () => {
      iconInput.value?.click()
    }

    const handleIconUpload = (event: Event) => {
      const target = event.target as HTMLInputElement
      const file = target.files?.[0]
      if (file) {
        const reader = new FileReader()
        reader.onload = (e) => {
          newCategory.value.icon = e.target?.result as string
        }
        reader.readAsDataURL(file)
      }
    }

    const removeIcon = () => {
      newCategory.value.icon = 'fas fa-folder'
      if (iconInput.value) {
        iconInput.value.value = ''
      }
    }

    const selectPredefinedIcon = (emoji: string) => {
      newCategory.value.icon = emoji
    }

    const saveCategory = () => {
      if (!newCategory.value.name) return

      const category: Category = {
        id: `CAT${Date.now()}`,
        icon: newCategory.value.icon || 'fas fa-folder', // Default icon if none selected
        name: newCategory.value.name,
        transactions: 0,
        status: newCategory.value.status
      }

      categories.value.push(category)
      closeCreateModal()
    }

    const predefinedIcons = [
      '🛒', '🍔', '🚗', '🏠', '💼', '🎮',
      '📱', '✈️', '🏥', '🎓', '🎁', '💳',
      '🏋️', '🎨', '🎵', '📚', '🎬', '🏖️',
      '🐾', '🌿', '💻', '📷', '🎪', '🎭'
    ]

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
      showEditModal,
      editingCategory,
      editIconInput,
      closeEditModal,
      triggerEditIconUpload,
      handleEditIconUpload,
      removeEditIcon,
      selectEditPredefinedIcon,
      updateCategory,
      showCreateModal,
      newCategory,
      iconInput,
      createCategory,
      closeCreateModal,
      triggerIconUpload,
      handleIconUpload,
      removeIcon,
      saveCategory,
      predefinedIcons,
      selectPredefinedIcon
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
