<template>
  <div>
    <input type="text" v-model="searchQuery" placeholder="Type to search..." />
    <p>Searching for: {{ debouncedSearchQuery }} - Updates: {{ updated }}</p>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'
import { refDebounced } from '@vueuse/core'

const searchQuery = ref('')
const debouncedSearchQuery = refDebounced(searchQuery, 300) // 300ms debounce
const updated = ref(0)
// Perform search API call here - triggers after 300ms inactivity
watch(debouncedSearchQuery, (newValue) => {
  updated.value += 1
  console.log('Searching for:', newValue)
})
</script>
