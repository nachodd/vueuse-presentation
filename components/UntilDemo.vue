<template>
  <div class="until-demo-container">
    <div class="counter-section">
      <p class="counter-text">
        Count: <span class="counter-value">{{ count }}</span>
      </p>
      <p class="counter-hint" v-if="count < targetValue">
        Add to {{ targetValue }} to show the alert
      </p>
      <p class="success-message" v-else>
        🎉 Target reached!
      </p>
    </div>

    <div class="controls-section">
      <button @click="decrement" class="control-button decrement">
        Decrement
      </button>
      <button @click="increment" class="control-button increment">
        Increment
      </button>
    </div>

    <div class="status-section" v-if="statusMessage !== ''">
      <div class="status-message" :class="{ 'status-success': reachedTarget }">
        {{ statusMessage }}
      </div>
      <button @click="resetDemo" class="reset-button">
        Reset Demo
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { until, invoke, useAsyncState } from '@vueuse/core'

const targetValue = 7
const count = ref(0)
const increment = () => count.value++
const decrement = () => count.value--

const reachedTarget = ref(false)
const statusMessage = ref('')


function resetDemo() {
  count.value = 0
  reachedTarget.value = false
  statusMessage.value = ''
}

const { state, isReady } = useAsyncState(
  fetch('https://jsonplaceholder.typicode.com/todos/1').then(t => t.json()),
  {},
)
invoke(async () => {
  await until(isReady).toBe(true)
  console.log("The response will be printed after isReady === true", state) // state is now ready!
})

invoke(async () => {
  try {
    await until(count).toMatch(v => v > targetValue, { timeout: 10000, throwOnTimeout: true })
    reachedTarget.value = true
    statusMessage.value = `Counter reached ${count.value}, which is greater than ${targetValue}!`
  } catch (e) {
    statusMessage.value = 'Operation timed out!'
    reachedTarget.value = false
  }
})
</script>

<style lang="postcss" scoped>
.until-demo-container {
  @apply flex flex-col items-center gap-6 p-6 bg-gray-800 rounded-lg shadow-md max-w-md mx-auto;
}

.counter-section {
  @apply text-center space-y-2;
}

.counter-text {
  @apply text-2xl font-medium text-white;
}

.counter-value {
  @apply font-bold text-blue-400;
}

.counter-hint {
  @apply text-gray-400 text-sm;
}

.success-message {
  @apply text-green-400 text-sm font-medium;
}

.controls-section {
  @apply flex gap-4;
}

.control-button {
  @apply px-4 py-2 rounded-md font-medium transition-all duration-300;
}

.increment {
  @apply bg-green-500 text-white hover:bg-green-600;
}

.decrement {
  @apply bg-red-500 text-white hover:bg-red-600;
}

.status-section {
  @apply w-full text-center space-y-4;
}

.status-message {
  @apply p-3 rounded-md text-sm;
  @apply bg-gray-700 text-gray-300;
}

.status-success {
  @apply bg-green-900/50 text-green-300;
}

.reset-button {
  @apply px-4 py-2 bg-gray-600 text-white rounded-md text-sm;
  @apply hover:bg-gray-700 transition-colors duration-300;
}
</style>
