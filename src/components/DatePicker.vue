<template>
  <div class="relative">
    <input
      ref="inputRef"
      type="text"
      :placeholder="placeholder"
      class="form-control py-2.5"
      :value="modelValue"
      @input="handleInput"
    />
    <svg
      class="absolute right-4 top-1/2 h-4 w-4 -translate-y-1/2 text-gray-400"
      xmlns="http://www.w3.org/2000/svg"
      viewBox="0 0 24 24"
      fill="none"
      stroke="currentColor"
      stroke-width="2"
      stroke-linecap="round"
      stroke-linejoin="round"
    >
      <rect x="3" y="4" width="18" height="18" rx="2" ry="2"></rect>
      <line x1="16" y1="2" x2="16" y2="6"></line>
      <line x1="8" y1="2" x2="8" y2="6"></line>
      <line x1="3" y1="10" x2="21" y2="10"></line>
    </svg>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, watch } from 'vue'
import flatpickr from 'flatpickr'
import 'flatpickr/dist/flatpickr.css'

const props = defineProps({
  modelValue: {
    type: String,
    default: ''
  },
  placeholder: {
    type: String,
    default: 'Select date'
  }
})

const emit = defineEmits(['update:modelValue'])

const inputRef = ref(null)
let flatpickrInstance = null

onMounted(() => {
  flatpickrInstance = flatpickr(inputRef.value, {
    dateFormat: 'Y-m-d',
    onChange: (selectedDates) => {
      if (selectedDates.length > 0) {
        emit('update:modelValue', selectedDates[0].toISOString().split('T')[0])
      }
    }
  })
})

onUnmounted(() => {
  if (flatpickrInstance) {
    flatpickrInstance.destroy()
  }
})

watch(() => props.modelValue, (newValue) => {
  if (flatpickrInstance && newValue !== flatpickrInstance.input.value) {
    flatpickrInstance.setDate(newValue)
  }
})

const handleInput = (event) => {
  emit('update:modelValue', event.target.value)
}
</script>

<style>
.form-control {
  @apply w-full rounded-lg border border-gray-200 bg-white px-4 text-theme-sm text-gray-500 outline-none transition-all duration-200 ease-in-out focus:border-brand-500 focus:ring-0 dark:border-white/10 dark:bg-white/5 dark:text-gray-400;
}

/* Flatpickr Custom Styles */
.flatpickr-calendar {
  @apply rounded-lg border border-gray-200 bg-white shadow-lg dark:border-white/10 dark:bg-gray-800;
}

.flatpickr-day {
  @apply text-theme-sm text-gray-700 hover:bg-gray-100 dark:text-gray-200 dark:hover:bg-gray-700;
}

.flatpickr-day.selected {
  @apply bg-brand-500 text-white hover:bg-brand-600 dark:bg-brand-500 dark:hover:bg-brand-600;
}

.flatpickr-day.today {
  @apply border-brand-500;
}

.flatpickr-months .flatpickr-month {
  @apply text-theme-sm text-gray-700 dark:text-gray-200;
}

.flatpickr-current-month .flatpickr-monthDropdown-months {
  @apply text-theme-sm text-gray-700 dark:text-gray-200;
}

.flatpickr-weekday {
  @apply text-theme-sm text-gray-500 dark:text-gray-400;
}

.flatpickr-months .flatpickr-prev-month,
.flatpickr-months .flatpickr-next-month {
  @apply text-gray-700 dark:text-gray-200;
}

.flatpickr-months .flatpickr-prev-month:hover svg,
.flatpickr-months .flatpickr-next-month:hover svg {
  @apply fill-brand-500;
}
</style> 