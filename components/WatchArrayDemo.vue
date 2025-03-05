<template>
  <div class="watch-array-container">
    <div class="array-display">
      <h3 class="array-title">Current Array:</h3>
      <div class="array-items">
        <div v-for="(item, index) in list" :key="index" class="array-item">
          {{ item }}
          <button @click="removeItem(index)" class="remove-button">×</button>
        </div>
      </div>
    </div>
    <div class="controls-section">
      <div class="input-group">
        <input
          type="number"
          v-model="newItem"
          class="number-input"
          placeholder="Enter a number"
        />
        <button @click="addItem" class="add-button">
          Add
        </button>
      </div>
    </div>
    <div class="changes-log">
      <h3 class="log-title">Changes Log:</h3>
      <div class="log-entries">
        <div v-for="(log, index) in changeLogs" :key="index" class="log-entry" :class="log.type">
          <span class="log-time">{{ log.time }}</span>
          <span class="log-message">{{ log.message }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { watchArray } from '@vueuse/core'

const list = ref([1, 2, 3])
const newItem = ref('')
const changeLogs = ref([])

function addLog(message, type = 'info') {
  const time = new Date().toLocaleTimeString()
  changeLogs.value.unshift({ message, time, type })
  if (changeLogs.value.length > 5) {
    changeLogs.value.pop()
  }
}

watchArray(list, (newList, oldList, added, removed) => {
  if (added.length) {
    addLog(`Added: ${added.join(', ')}`, 'added')
  }
  if (removed.length) {
    addLog(`Removed: ${removed.join(', ')}`, 'removed')
  }
  addLog(`New List: ${newList.join(', ')}`)
})

function addItem() {
  if (newItem.value !== '') {
    list.value = [...list.value, Number(newItem.value)]
    newItem.value = ''
  }
}

function removeItem(index) {
  list.value = list.value.filter((_, i) => i !== index)
}
</script>

<style lang="postcss" scoped>
.watch-array-container {
  @apply flex flex-col gap-0 p-3 bg-gray-800 rounded-lg shadow-lg max-w-md mx-auto font-size-xs line-height-3;
}

.array-display {
  @apply space-y-3 mb-2;
}

.array-title {
  @apply text-lg font-semibold text-white;
}

.array-items {
  @apply flex flex-wrap gap-1;
}

.array-item {
  @apply flex items-center gap-2 px-1 py-1 bg-gray-700 text-white rounded-md;
}

.remove-button {
  @apply text-red-400 hover:text-red-300 font-bold text-lg p-1 line-height-1 font-size-xs;
}

.controls-section {
  @apply space-y-3;
}

.input-group {
  @apply flex gap-2;
}

.number-input {
  @apply flex-1 px-3 py-2 bg-gray-700 text-white rounded-md border border-gray-600;
  @apply focus:outline-none focus:border-blue-500;
}

.add-button {
  @apply px-4 py-2 bg-green-600 text-white rounded-md;
  @apply hover:bg-green-700 transition-colors duration-300;
}

.changes-log {
  @apply space-y-3;
}

.log-title {
  @apply text-lg font-semibold text-white;
}

.log-entries {
  @apply space-y-2;
}

.log-entry {
  @apply flex gap-3 px-3 py-2 rounded-md text-sm;
  @apply bg-gray-700 text-gray-300;
}

.log-entry.added {
  @apply bg-green-900/50 text-green-300;
}

.log-entry.removed {
  @apply bg-red-900/50 text-red-300;
}

.log-time {
  @apply text-gray-400;
}

.log-message {
  @apply flex-1;
}
</style>
