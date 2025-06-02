<template>
  <admin-layout>
    <div class="mx-auto  max-w-screen-2xl md:p-6 2xl:p-10">
      <div class="flex flex-col gap-3 mb-6 sm:flex-row sm:items-center sm:justify-between">
        <h2 class="font-bold text-black text-title-md2 dark:text-white">Failed SMS</h2>
      </div>
      <failed-s-m-s-table />
    </div>
  </admin-layout>
</template>

<script lang="ts" setup>
import AdminLayout from '@/components/layout/AdminLayout.vue'
import FailedSMSTable from '@/components/failed-sms/FailedSMSTable.vue'
</script> 