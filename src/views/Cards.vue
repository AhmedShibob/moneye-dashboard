<template>
  <admin-layout>
    <div class="p-4">
      <h1 class="text-2xl font-semibold text-gray-800 dark:text-white">Cards</h1>
    </div>
    <cards-table />
  </admin-layout>
</template>

<script lang="ts">
import AdminLayout from '../components/layout/AdminLayout.vue'
import CardsTable from '@/components/cards/CardsTable.vue'

export default {
  name: 'CardManagement',
  components: {
    AdminLayout,
    CardsTable
  }
}
</script> 