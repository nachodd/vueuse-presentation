<template>
  <div class="computed-control-container">
    <div class="values-section">
      <div class="value-group">
        <div class="value-label">Source Value:</div>
        <div class="value-controls">
          <input v-model="source" class="value-input" />
        </div>
      </div>

      <div class="value-group">
        <div class="value-label">Counter Value:</div>
        <div class="value-controls">
          <button @click="counter--" class="control-button decrement">-</button>
          <span class="counter-display">{{ counter }}</span>
          <button @click="counter++" class="control-button increment">+</button>
        </div>
      </div>
    </div>

    <div class="computed-section">
      <div class="computed-display">
        <div class="computed-label">Computed Result:</div>
        <div class="computed-value">{{ computedRef }}</div>
      </div>
      <div class="computed-controls">
        <button @click="forceUpdate" class="action-button">
          Force Update
        </button>
      </div>
    </div>

    <div class="info-section">
      <p class="info-text">
        The computed value only updates when the source changes or when manually triggered.
        Try changing both values to see the difference.
      </p>
      <ul class="update-log">
        <li v-for="(log, index) in updateLogs" :key="index" class="log-entry">
          {{ log }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'
import { computedWithControl } from '@vueuse/core'

const source = ref('foo')
const counter = ref(0)
const updateLogs = ref([])

function addLog(message) {
  updateLogs.value.unshift(`${new Date().toLocaleTimeString()}: ${message}`)
  if (updateLogs.value.length > 3) updateLogs.value.pop()
}

const computedRef = computedWithControl(
  () => source.value, // watch source
  () => `Source: ${source.value}, Counter: ${counter.value}` // computed getter
)

// Watch for changes to demonstrate behavior
watch(source, () => {
  addLog('Source changed - computed will update')
})

watch(counter, () => {
  addLog('Counter changed - computed will NOT update')
})

watch(computedRef, () => {
  addLog('Computed value updated')
})

// Manual trigger when needed
function forceUpdate() {
  computedRef.trigger()
}
</script>

<style lang="postcss" scoped>
.computed-control-container {
  @apply flex flex-col gap-3 p-3 bg-gray-800 rounded-lg shadow-lg max-w-md mx-auto;
}

.values-section {
  @apply space-y-2;
}

.value-group {
  @apply flex items-center justify-between gap-4 p-4 bg-gray-700 rounded-lg;
}

.value-label {
  @apply texs-sm text-gray-300 font-medium;
}

.value-controls {
  @apply flex items-center gap-2;
}

.value-input {
  @apply px-3 py-2 bg-gray-600 border border-gray-500 rounded text-white;
  @apply focus:outline-none focus:border-blue-500;
}

.counter-display {
  @apply min-w-[3rem] text-center text-white font-bold;
}

.control-button {
  @apply line-height-4 font-bold text-lg;
  @apply transition-colors duration-300;
}

.increment {
  @apply bg-green-600 text-white hover:bg-green-700;
}

.decrement {
  @apply bg-red-600 text-white hover:bg-red-700;
}

.computed-section {
  @apply flex items-center justify-between gap-2 p-2 bg-gray-700 rounded-lg;
}

.computed-display {
  @apply flex flex-col gap-1;
}

.computed-label {
  @apply text-xs text-gray-300;
}

.computed-value {
  @apply text-sm font-bold text-blue-400;
}

.action-button {
  @apply bg-blue-600 text-white rounded-md text-xs;
  @apply hover:bg-blue-700 transition-colors duration-300;
}

.info-section {
  @apply space-y-2;
}

.info-text {
  @apply text-xs text-gray-400;
}

.update-log {
  @apply text-xs mt-1 space-y-2;
}

.log-entry {
  @apply text-xs text-gray-400 font-mono;
}
</style>
