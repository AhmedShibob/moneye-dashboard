<template>
  <div class="rounded-xl border border-gray-200 bg-white p-6 dark:border-white/10 dark:bg-white/5">
    <div class="mb-6 flex flex-col gap-4 sm:flex-row sm:items-center sm:justify-between">
      <h2 class="text-xl font-semibold text-gray-900 dark:text-white">User List</h2>
      <div class="flex items-center gap-3">
        <button
          class="btn btn-primary"
          @click="openAddUserModal"
        >
          <span class="flex items-center gap-2">
            <svg
              class="h-5 w-5"
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
            Add User
          </span>
        </button>
      </div>
    </div>

    <div class="mb-6">
      <UserFilter
        @search="handleSearch"
        @filter="handleFilter"
        @sort="handleSort"
      />
    </div>

    <div class="overflow-x-auto">
      <table class="w-full">
        <thead>
          <tr class="border-b border-gray-200 dark:border-white/10">
            <th class="pb-3 text-left text-theme-sm font-medium text-gray-500 dark:text-gray-400">Name</th>
            <th class="pb-3 text-left text-theme-sm font-medium text-gray-500 dark:text-gray-400">Role</th>
            <th class="pb-3 text-left text-theme-sm font-medium text-gray-500 dark:text-gray-400">Status</th>
            <th class="pb-3 text-left text-theme-sm font-medium text-gray-500 dark:text-gray-400">Last Active</th>
            <th class="pb-3 text-right text-theme-sm font-medium text-gray-500 dark:text-gray-400">Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="user in filteredUsers"
            :key="user.id"
            class="border-b border-gray-200 dark:border-white/10"
          >
            <td class="py-4">
              <div class="flex items-center gap-3">
                <img
                  :src="user.avatar"
                  :alt="user.name"
                  class="h-10 w-10 rounded-full"
                />
                <div>
                  <h3 class="text-theme-sm font-medium text-gray-900 dark:text-white">
                    {{ user.name }}
                  </h3>
                  <p class="text-theme-xs text-gray-500 dark:text-gray-400">
                    {{ user.email }}
                  </p>
                </div>
              </div>
            </td>
            <td class="py-4">
              <span
                class="inline-flex items-center rounded-full px-2.5 py-0.5 text-theme-xs font-medium"
                :class="{
                  'bg-blue-100 text-blue-800 dark:bg-blue-900/30 dark:text-blue-400': user.role === 'admin',
                  'bg-green-100 text-green-800 dark:bg-green-900/30 dark:text-green-400': user.role === 'user',
                  'bg-purple-100 text-purple-800 dark:bg-purple-900/30 dark:text-purple-400': user.role === 'editor'
                }"
              >
                {{ user.role }}
              </span>
            </td>
            <td class="py-4">
              <span
                class="inline-flex items-center rounded-full px-2.5 py-0.5 text-theme-xs font-medium"
                :class="{
                  'bg-green-100 text-green-800 dark:bg-green-900/30 dark:text-green-400': user.status === 'active',
                  'bg-red-100 text-red-800 dark:bg-red-900/30 dark:text-red-400': user.status === 'inactive',
                  'bg-yellow-100 text-yellow-800 dark:bg-yellow-900/30 dark:text-yellow-400': user.status === 'pending'
                }"
              >
                {{ user.status }}
              </span>
            </td>
            <td class="py-4 text-theme-sm text-gray-500 dark:text-gray-400">
              {{ user.lastActive }}
            </td>
            <td class="py-4 text-right">
              <div class="flex items-center justify-end gap-2">
                <button
                  class="btn btn-icon btn-soft-primary"
                  @click="editUser(user)"
                >
                  <svg
                    class="h-5 w-5"
                    viewBox="0 0 24 24"
                    fill="none"
                    xmlns="http://www.w3.org/2000/svg"
                  >
                    <path
                      d="M11 4H4C3.46957 4 2.96086 4.21071 2.58579 4.58579C2.21071 4.96086 2 5.46957 2 6V20C2 20.5304 2.21071 21.0391 2.58579 21.4142C2.96086 21.7893 3.46957 22 4 22H18C18.5304 22 19.0391 21.7893 19.4142 21.4142C19.7893 21.0391 20 20.5304 20 20V13"
                      stroke="currentColor"
                      stroke-width="2"
                      stroke-linecap="round"
                      stroke-linejoin="round"
                    />
                    <path
                      d="M18.5 2.50001C18.8978 2.10219 19.4374 1.87869 20 1.87869C20.5626 1.87869 21.1022 2.10219 21.5 2.50001C21.8978 2.89784 22.1213 3.4374 22.1213 4.00001C22.1213 4.56262 21.8978 5.10219 21.5 5.50001L12 15L8 16L9 12L18.5 2.50001Z"
                      stroke="currentColor"
                      stroke-width="2"
                      stroke-linecap="round"
                      stroke-linejoin="round"
                    />
                  </svg>
                </button>
                <button
                  class="btn btn-icon btn-soft-danger"
                  @click="deleteUser(user)"
                >
                  <svg
                    class="h-5 w-5"
                    viewBox="0 0 24 24"
                    fill="none"
                    xmlns="http://www.w3.org/2000/svg"
                  >
                    <path
                      d="M3 6H5H21"
                      stroke="currentColor"
                      stroke-width="2"
                      stroke-linecap="round"
                      stroke-linejoin="round"
                    />
                    <path
                      d="M19 6V20C19 20.5304 18.7893 21.0391 18.4142 21.4142C18.0391 21.7893 17.5304 22 17 22H7C6.46957 22 5.96086 21.7893 5.58579 21.4142C5.21071 21.0391 5 20.5304 5 20V6M8 6V4C8 3.46957 8.21071 2.96086 8.58579 2.58579C8.96086 2.21071 9.46957 2 10 2H14C14.5304 2 15.0391 2.21071 15.4142 2.58579C15.7893 2.96086 16 3.46957 16 4V6"
                      stroke="currentColor"
                      stroke-width="2"
                      stroke-linecap="round"
                      stroke-linejoin="round"
                    />
                  </svg>
                </button>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import UserFilter from './UserFilter.vue'

const users = ref([
  {
    id: 1,
    name: 'John Doe',
    email: 'john@example.com',
    role: 'admin',
    status: 'active',
    lastActive: '2 hours ago',
    avatar: 'https://i.pravatar.cc/150?img=1'
  },
  {
    id: 2,
    name: 'Jane Smith',
    email: 'jane@example.com',
    role: 'user',
    status: 'inactive',
    lastActive: '1 day ago',
    avatar: 'https://i.pravatar.cc/150?img=2'
  },
  {
    id: 3,
    name: 'Mike Johnson',
    email: 'mike@example.com',
    role: 'editor',
    status: 'pending',
    lastActive: '3 days ago',
    avatar: 'https://i.pravatar.cc/150?img=3'
  }
])

const searchQuery = ref('')
const selectedRole = ref('')
const selectedStatus = ref('')
const sortBy = ref('name')

const filteredUsers = computed(() => {
  let result = [...users.value]

  // Apply search filter
  if (searchQuery.value) {
    const query = searchQuery.value.toLowerCase()
    result = result.filter(user => 
      user.name.toLowerCase().includes(query) ||
      user.email.toLowerCase().includes(query)
    )
  }

  // Apply role filter
  if (selectedRole.value) {
    result = result.filter(user => user.role === selectedRole.value)
  }

  // Apply status filter
  if (selectedStatus.value) {
    result = result.filter(user => user.status === selectedStatus.value)
  }

  // Apply sorting
  result.sort((a, b) => {
    switch (sortBy.value) {
      case 'name':
        return a.name.localeCompare(b.name)
      case 'role':
        return a.role.localeCompare(b.role)
      case 'status':
        return a.status.localeCompare(b.status)
      case 'date':
        return new Date(b.lastActive) - new Date(a.lastActive)
      default:
        return 0
    }
  })

  return result
})

const handleSearch = (query) => {
  searchQuery.value = query
}

const handleFilter = (filters) => {
  selectedRole.value = filters.role
  selectedStatus.value = filters.status
}

const handleSort = (sort) => {
  sortBy.value = sort
}

const openAddUserModal = () => {
  // Implement add user modal logic
}

const editUser = (user) => {
  // Implement edit user logic
}

const deleteUser = (user) => {
  // Implement delete user logic
}
</script>

<style scoped>
.btn {
  @apply inline-flex items-center justify-center rounded-lg px-4 py-2 text-theme-sm font-medium transition-all duration-200 ease-in-out;
}

.btn-primary {
  @apply bg-brand-500 text-white hover:bg-brand-600 focus:ring-2 focus:ring-brand-500/20;
}

.btn-icon {
  @apply h-9 w-9 rounded-lg p-0;
}

.btn-soft-primary {
  @apply bg-brand-50 text-brand-500 hover:bg-brand-100 dark:bg-brand-500/10 dark:text-brand-400 dark:hover:bg-brand-500/20;
}

.btn-soft-danger {
  @apply bg-red-50 text-red-500 hover:bg-red-100 dark:bg-red-500/10 dark:text-red-400 dark:hover:bg-red-500/20;
}
</style> 