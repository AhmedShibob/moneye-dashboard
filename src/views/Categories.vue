<template>
  <admin-layout>
    <div class="p-4">
      <h1 class="text-2xl font-semibold text-gray-800 dark:text-white">Categories</h1>
    </div>
    <categories-table />
  </admin-layout>
</template>

<script lang="ts" setup>
import AdminLayout from '../components/layout/AdminLayout.vue'
import CategoriesTable from '../components/categories/CategoriesTable.vue'

</script> 