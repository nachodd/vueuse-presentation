<template>
  <div class="computed-async-container">
    <div class="input-section">
      <label class="input-label">
        Package Name:
        <input
          v-model="packageName"
          class="package-input"
          placeholder="e.g., @vueuse/core"
        />
      </label>
      <div class="status-badge" :class="{ 'is-evaluating': evaluating }">
        {{ evaluating ? 'Loading...' : 'Ready' }}
      </div>
    </div>

    <div class="result-section">
      <h3 class="result-title">NPM Downloads (Last Week):</h3>
      <div class="result-value">
        {{ downloads }}
      </div>
    </div>

    <div class="info-section">
      <p class="info-text">
        Type a package name above to see its weekly downloads.
        The value will update automatically after you stop typing.
      </p>
    </div>
  </div>
</template>

<script setup>
import { ref, shallowRef } from 'vue'
import { computedAsync } from '@vueuse/core'

const packageName = shallowRef('@vueuse/core')
const evaluating = ref(false)

const downloads = computedAsync(
  async (onCancel) => {
    const abortController = new AbortController()
    onCancel(() => abortController.abort())

    try {
      const response = await fetch(
        `https://api.npmjs.org/downloads/point/last-week/${packageName.value}`,
        { signal: abortController.signal }
      )

      if (!response.ok)
        return '—'

      const data = await response.json()
      return new Intl.NumberFormat().format(data.downloads)
    } catch {
      return '—'
    }
  },
  '—',
  { evaluating }
)
</script>

<style lang="postcss" scoped>
.computed-async-container {
  @apply flex flex-col gap-6 p-6 bg-gray-800 rounded-lg shadow-lg max-w-md mx-auto;
}

.input-section {
  @apply flex items-center gap-4;
}

.input-label {
  @apply flex-1 text-white font-medium;
}

.package-input {
  @apply mt-1 block w-full px-3 py-2 bg-gray-700 border border-gray-600 rounded-md text-white;
  @apply focus:outline-none focus:border-blue-500;
}

.status-badge {
  @apply px-3 py-1 rounded-full text-sm font-medium;
  @apply bg-green-900/50 text-green-300;
}

.status-badge.is-evaluating {
  @apply bg-yellow-900/50 text-yellow-300;
}

.result-section {
  @apply text-center;
}

.result-title {
  @apply text-lg font-semibold text-white mb-2;
}

.result-value {
  @apply text-3xl font-bold text-blue-400;
}

.info-section {
  @apply text-center;
}

.info-text {
  @apply text-sm text-gray-400;
}
</style>
