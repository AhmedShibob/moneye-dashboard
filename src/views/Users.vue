<template>
  <admin-layout>
    <div class="mx-auto max-w-screen-2xl p-4 md:p-6 2xl:p-10">
      <div class="mb-6 flex flex-col gap-3 sm:flex-row sm:items-center sm:justify-between">
        <h2 class="text-title-md2 font-bold text-black dark:text-white">Users</h2>
      </div>
      <users-table />
    </div>
  </admin-layout>
</template>

<script lang="ts">
import AdminLayout from '@/components/layout/AdminLayout.vue'
import UsersTable from '@/components/users/UsersTable.vue'

export default {
  name: 'UsersManagement',
  components: {
    AdminLayout,
    UsersTable,
  },
}
</script> 