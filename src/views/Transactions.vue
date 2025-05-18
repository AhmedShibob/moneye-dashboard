<template>
  <admin-layout>
    <div class="p-4">
      <h1 class="text-2xl font-semibold text-gray-800 dark:text-white">Transactions</h1>
    </div>
    <transactions-table />
  </admin-layout>
</template>

<script lang="ts" setup>
import AdminLayout from '../components/layout/AdminLayout.vue'
import TransactionsTable from '../components/transactions/TransactionsTable.vue'

</script> 