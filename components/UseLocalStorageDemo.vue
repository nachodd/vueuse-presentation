<template>
  <div>
    <span>Persistent Theme Switcher: </span>
    <button @click="toggleTheme">
      Switch to {{ theme === 'light' ? 'Dark' : 'Light' }} Theme
    </button>
    <p>Current theme: {{ theme }} (Refresh the page - your preference stays!)</p>
  </div>
</template>

<script setup>
import { useLocalStorage } from '@vueuse/core'
// Persists to localStorage automatically
const theme = useLocalStorage('theme', 'light')
function toggleTheme() {
  theme.value = theme.value === 'light' ? 'dark' : 'light'
  // Apply theme to document
  document.body.parentElement.className = theme.value
}
</script>
