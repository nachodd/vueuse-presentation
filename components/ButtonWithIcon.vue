<script setup>
import { createReusableTemplate } from '@vueuse/core'

const [DefineTemplate, ReusableTemplate] = createReusableTemplate()

defineProps({
  iconPosition: {
    type: String,
    default: 'left', // could be 'left' or 'right'
  },
})
</script>

<template>
  <!-- Define the reusable icon template -->
  <DefineTemplate>
    <svg class="w-5 h-5 text-white" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
      <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7"></path>
    </svg>
  </DefineTemplate>

  <button class="btn">
    <template v-if="iconPosition === 'left'">
      <ReusableTemplate />
    </template>
    <span><slot></slot></span>
    <template v-if="iconPosition === 'right'">
      <ReusableTemplate />
    </template>
  </button>
</template>

<style scoped>
.btn {
  @apply flex items-center gap-2 px-4 py-2 font-medium text-white bg-blue-600 hover:bg-blue-700 rounded-lg transition;
}
</style>
