<template>
  <div class="reactify-demo">
    <div class="cont">
      <div class="input-section">
        <div class="input-group">
          <label>Side A:</label>
          <div class="number-input">
            <button @click="a--" class="control-btn">-</button>
            <input v-model="a" type="number" class="value-input" />
            <button @click="a++" class="control-btn">+</button>
          </div>
        </div>

        <div class="input-group">
          <label>Side B:</label>
          <div class="number-input">
            <button @click="b--" class="control-btn">-</button>
            <input v-model="b" type="number" class="value-input" />
            <button @click="b++" class="control-btn">+</button>
          </div>
        </div>
      </div>
      <div class="result">
        <p class="result-text">Hypotenuse: <br><span class="side-c">c = {{ c.toFixed(2) }}</span></p>
        <p class="formula">√(<span class="side-a">a</span>² + <span class="side-b">b</span>²) = √(<span class="side-a">{{ a }}</span>² + <span class="side-b">{{ b }}</span>²)</p>
      </div>
    </div>


    <div class="result-section">
      <div class="triangle-visualization">
        <svg viewBox="0 0 100 100" class="triangle-svg">
          <!-- Base (side a) -->
          <line
            :x1="0"
            :y1="triangleHeight"
            :x2="triangleWidth"
            :y2="triangleHeight"
            stroke="#FF6B6B"
            stroke-width="2"
          />
          <!-- Height (side b) -->
          <line
            :x1="0"
            :y1="0"
            :x2="0"
            :y2="triangleHeight"
            stroke="#4ECDC4"
            stroke-width="2"
          />
          <!-- Hypotenuse (side c) -->
          <line
            :x1="0"
            :y1="0"
            :x2="triangleWidth"
            :y2="triangleHeight"
            stroke="#FFD166"
            stroke-width="2"
          />

          <!-- Labels -->
          <text :x="triangleWidth/2" :y="triangleHeight + 15" text-anchor="middle" class="side-label" fill="#FF6B6B">a = {{ a }}</text>
          <text x="-15" :y="triangleHeight/2" text-anchor="middle" class="side-label" fill="#4ECDC4" transform="rotate(-90 -15 50)">b = {{ b }}</text>
          <text :x="triangleWidth/2 - 10" :y="triangleHeight/2 - 10" text-anchor="middle" class="side-label hypotenuse-label" fill="#FFD166">c = {{ c.toFixed(2) }}</text>

          <!-- Right angle marker -->
          <path
            :d="`M 0,${triangleHeight-10} L 0,${triangleHeight} L 10,${triangleHeight}`"
            fill="none"
            stroke="white"
            stroke-width="1"
          />
        </svg>
      </div>

    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { reactify } from '@vueuse/core'

// Create reactive math functions
const pow = reactify(Math.pow)
const sqrt = reactify(Math.sqrt)
const add = reactify((a, b) => a + b)

// Input values
const a = ref(3)
const b = ref(4)

// Calculate hypotenuse using reactive functions
const c = sqrt(add(pow(a, 2), pow(b, 2)))

// Calculate triangle dimensions for SVG
const triangleWidth = computed(() => {
  const ratio = a.value / Math.max(a.value, b.value)
  return Math.min(100, 100 * ratio)
})

const triangleHeight = computed(() => {
  const ratio = b.value / Math.max(a.value, b.value)
  return Math.min(100, 100 * ratio)
})
</script>

<style lang="postcss" scoped>
.reactify-demo {
  @apply flex flex-col gap-6 p-6 bg-gray-800 rounded-lg;
}

.cont {
  @apply flex flex-row gap-4;
}

.input-section {
  @apply flex flex-col flex-grow gap-2 line-height-2;
}

.input-group {
  @apply flex items-center justify-between gap-2 p-2 bg-gray-700 rounded-lg;

  label {
    @apply text-xs text-gray-300 font-medium whitespace-nowrap;
  }
}

.number-input {
  @apply flex items-center gap-2 text-xs;
}

.control-btn {
  @apply px-2 py-1 bg-blue-600 text-white rounded-md text-xs font-bold;
  @apply hover:bg-blue-700 transition-colors duration-300;
}

.value-input {
  @apply w-16 px-2 py-1 bg-gray-600 border border-gray-500 rounded text-center text-white;
  @apply focus:outline-none focus:border-blue-500;
}

.result-section {
  @apply flex flex-col items-center gap-4;
}

.triangle-visualization {
  @apply w-64 h-64 flex items-center justify-center;
}

.triangle-svg {
  width: 100vw;
  height: 100vh;
  position: fixed;
  transform: scale(0.2);
}

.side-label {
  font-size: 10px;
  font-weight: bold;
}

.hypotenuse-label {
  font-weight: bold;
}

.result {
  @apply text-center;

  .result-text {
    @apply text-lg font-bold text-blue-400;
  }

  .formula {
    @apply text-sm text-gray-400 mt-2;
  }

  .side-a {
    color: #FF6B6B;
    font-weight: bold;
  }

  .side-b {
    color: #4ECDC4;
    font-weight: bold;
  }

  .side-c {
    color: #FFD166;
    font-weight: bold;
  }
}
</style>
