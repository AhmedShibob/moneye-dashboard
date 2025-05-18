<template>
  <div class="grid grid-cols-1 gap-4 sm:grid-cols-2">
    <!-- Left Column -->
    <div class="space-y-4">
      <div class="flex flex-col gap-2">
        <label for="search" class="text-theme-sm font-medium text-gray-700 dark:text-gray-200">Search</label>
        <input
          id="search"
          type="text"
          placeholder="Search by name or email..."
          class="form-control py-2.5"
          v-model="searchQuery"
          @input="handleSearch"
        />
      </div>

      <div class="flex flex-col gap-2">
        <label for="joinDate" class="text-theme-sm font-medium text-gray-700 dark:text-gray-200">Join Date</label>
        <DatePicker
          id="joinDate"
          v-model="joinDate"
          placeholder="Select join date"
          @update:modelValue="handleFilter"
        />
      </div>
    </div>

    <!-- Right Column -->
    <div class="space-y-4">
      <div class="flex flex-col gap-2">
        <label for="transactionCount" class="text-theme-sm font-medium text-gray-700 dark:text-gray-200">Transaction Count</label>
        <select
          id="transactionCount"
          class="form-control py-2.5"
          v-model="transactionCount"
          @change="handleFilter"
        >
          <option value="">All Transactions</option>
          <option value="0-10">0-10 Transactions</option>
          <option value="11-50">11-50 Transactions</option>
          <option value="51-100">51-100 Transactions</option>
          <option value="101-500">101-500 Transactions</option>
          <option value="501+">501+ Transactions</option>
        </select>
      </div>

      <div class="flex flex-col gap-2">
        <label for="sort" class="text-theme-sm font-medium text-gray-700 dark:text-gray-200">Sort By</label>
        <select
          id="sort"
          class="form-control py-2.5"
          v-model="sortBy"
          @change="handleSort"
        >
          <option value="name">Name</option>
          <option value="joinDate">Join Date</option>
          <option value="transactionCount">Transaction Count</option>
        </select>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import DatePicker from './DatePicker.vue'

const emit = defineEmits(['filter', 'search', 'sort'])

const searchQuery = ref('')
const joinDate = ref('')
const transactionCount = ref('')
const sortBy = ref('name')

const handleSearch = () => {
  emit('search', searchQuery.value)
}

const handleFilter = () => {
  emit('filter', {
    joinDate: joinDate.value,
    transactionCount: transactionCount.value
  })
}

const handleSort = () => {
  emit('sort', sortBy.value)
}
</script>

<style scoped>
.form-control {
  @apply w-full rounded-lg border border-gray-200 bg-white px-4 text-theme-sm text-gray-500 outline-none transition-all duration-200 ease-in-out focus:border-brand-500 focus:ring-0 dark:border-white/10 dark:bg-white/5 dark:text-gray-400;
}
</style> 