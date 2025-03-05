<template>
  <div class="async-state-container">
    <div class="status-section">
      <p class="status-text">
        Ready: <span :class="{ 'status-true': isReady, 'status-false': !isReady }">{{ isReady }}</span>
      </p>
      <p class="status-text">
        Loading: <span :class="{ 'status-true': isLoading, 'status-false': !isLoading }">{{ isLoading }}</span>
      </p>
    </div>

    <div class="data-section">
      <pre class="data-display">{{ state }}</pre>
    </div>

    <button @click="execute(2000, {id: currentId++})" class="execute-button" :disabled="isLoading">
      {{ isLoading ? 'Loading...' : 'Execute' }}
    </button>
  </div>
</template>

<script setup>
import {ref} from 'vue'
import { useAsyncState } from '@vueuse/core'

const currentId = ref(1)

async function fetchTodo(id) {
  const response = await fetch(`https://jsonplaceholder.typicode.com/todos/${id}`)
  return response.json()
}

const { state, isReady, isLoading, execute } = useAsyncState(
  ({id}) => fetchTodo(id),
  { id: null, title: '', completed: false },
  {
    immediate: false,
  }
)
</script>

<style lang="postcss" scoped>
.async-state-container {
  @apply flex flex-col items-center gap-6 p-6 bg-gray-800 rounded-lg shadow-md max-w-md mx-auto;
}

.status-section {
  @apply w-full space-y-2;
}

.status-text {
  @apply text-lg font-medium;
}

.status-true {
  @apply text-green-600 font-semibold;
}

.status-false {
  @apply text-red-600 font-semibold;
}

.data-section {
  @apply w-full;
}

.data-display {
  @apply p-4 rounded-md text-sm font-mono overflow-auto max-h-48;
}

.execute-button {
  @apply px-6 py-2 bg-blue-500 text-white rounded-md font-medium;
  @apply transition-colors duration-300;
  @apply hover:bg-blue-600;
  @apply disabled:bg-blue-300 disabled:cursor-not-allowed;
}
</style>
