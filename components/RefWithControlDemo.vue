<template>
  <div class="ref-control-demo">
    <div class="search-section">
      <div class="input-group">
        <input
          v-model="searchInput"
          @input="handleInput"
          type="text"
          placeholder="Type to search..."
          class="search-input"
        />
        <div class="status-badge" :class="{ 'is-pending': isPending }">
          {{ isPending ? 'Updating...' : 'Ready' }}
        </div>
      </div>

      <div class="controls">
        <button @click="triggerUpdate" class="control-btn">
          Force Update
        </button>
        <button @click="silentUpdate" class="control-btn">
          Silent Update
        </button>
        <button @click="peekValue" class="control-btn">
          Peek Value
        </button>
      </div>
    </div>

    <div class="results-section">
      <div class="status-info">
        <p>Current Value: <span class="value">{{ controlledValue.value }}</span></p>
        <p>Computed Value: <span class="value">{{ computedValue }}</span></p>
        <p>Is Pending: <span class="value">{{ isPending }}</span></p>
      </div>

      <div class="update-log">
        <h3>Update Log:</h3>
        <ul class="log-list">
          <li v-for="(log, index) in updateLogs" :key="index" class="log-item">
            {{ log }}
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { refWithControl } from '@vueuse/core'
import { formatDate } from '../utils/date'

const searchInput = ref('')
const updateLogs = ref([])
const isPending = ref(false)

const addLog = (message) => {
  updateLogs.value.unshift(`${formatDate(new Date())} - ${message}`)
  if (updateLogs.value.length > 5) updateLogs.value.pop()
}

// Create a controlled ref
const controlledValue = refWithControl(searchInput.value, {
  onBeforeChange(value, oldValue) {
    isPending.value = true
    addLog(`Before change: "${oldValue}" → "${value}"`)

    // Simulate async validation
    setTimeout(() => {
      isPending.value = false
      addLog(`Change completed: "${value}"`)
    }, 500)

    // Demonstrate validation - reject empty values
    if (value.trim() === '') {
      addLog(`Change rejected: Empty value not allowed`)
      return false
    }

    return true
  },
  onChanged(value, oldValue) {
    addLog(`Value changed: "${oldValue}" → "${value}"`)
  }
})

// Watch for input changes and update controlled value
const updateControlledValue = () => {
  controlledValue.value = searchInput.value
}

// Create a computed value that depends on the controlled ref
const computedValue = computed(() => {
  // This will only update when controlledValue changes reactively
  return controlledValue.value ? `Processed: ${controlledValue.value.toUpperCase()}` : ''
})

// Demonstrate different ways to update the value
const triggerUpdate = () => {
  // Normal reactive update
  controlledValue.value = searchInput.value
  addLog(`Manual update triggered (reactive)`)
}

const silentUpdate = () => {
  // Update without triggering reactivity
  controlledValue.silentSet(searchInput.value)
  addLog(`Silent update: "${searchInput.value}" (non-reactive)`)
}

const peekValue = () => {
  // Get value without tracking
  const value = controlledValue.peek()
  addLog(`Peeked value: "${value}" (non-tracking)`)
}

// Watch for input changes
const debouncedUpdate = () => {
  let timeout
  return () => {
    clearTimeout(timeout)
    timeout = setTimeout(() => {
      updateControlledValue()
    }, 300)
  }
}

const handleInput = debouncedUpdate()
</script>

<style lang="postcss" scoped>
.ref-control-demo {
  @apply flex flex-col gap-6 p-2 bg-gray-800 rounded-lg;
}

.search-section {
  @apply flex flex-col gap-4;
}

.input-group {
  @apply flex items-center gap-4;
}

.search-input {
  @apply flex-1 px-4 py-2 bg-gray-700 border border-gray-600 rounded-lg text-white;
  @apply focus:outline-none focus:border-blue-500;
}

.status-badge {
  @apply px-3 py-1 text-sm rounded-full bg-green-600 text-white;
  @apply transition-colors duration-300;

  &.is-pending {
    @apply bg-yellow-600;
  }
}

.controls {
  @apply flex gap-2 flex-wrap;
}

.control-btn {
  @apply px-4 py-2 bg-blue-600 text-white rounded-lg text-sm;
  @apply hover:bg-blue-700 transition-colors duration-300;
}

.results-section {
  @apply flex flex-col gap-4;
}

.status-info {
  @apply space-y-2 p-2 bg-gray-700 rounded-lg;

  p {
    @apply text-sm text-gray-300;
  }

  .value {
    @apply font-mono text-blue-400;
  }
}

.update-log {
  @apply p-2 bg-gray-700 rounded-lg;

  h3 {
    @apply text-sm font-semibold text-gray-300 mb-0;
  }
}

.log-list {
  @apply space-y-0;
}

.log-item {
  @apply text-sm text-gray-400 font-mono;
}
</style>
