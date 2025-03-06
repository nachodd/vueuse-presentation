<template>
  <div class="click-outside-container">
    <button @click="toggle" class="toggle-btn">
      {{ open ? 'Close Menu' : 'Open Menu' }}
    </button>

    <div v-if="open" ref="dropdown" class="dropdown">
      <div class="dropdown-header">Click outside to close</div>
      <div class="dropdown-content">
        <div class="dropdown-item">Profile</div>
        <div class="dropdown-item">Settings</div>
        <div class="dropdown-item">Help</div>
        <div class="dropdown-item">Logout</div>
      </div>
      <div class="click-count">Times closed: {{ closeCount }}</div>
    </div>
  </div>
</template>

<script setup>
import { ref, useTemplateRef } from 'vue'
import { onClickOutside } from '@vueuse/core'

const dropdown = useTemplateRef('dropdown')
const open = ref(false)
const closeCount = ref(0)

function toggle() {
  open.value = !open.value
}
// Use onClickOutside to detect clicks outside the dropdown
onClickOutside(dropdown, () => {
  if (open.value) {
    open.value = false
    closeCount.value++
  }
}, { ignore: ['.toggle-btn'] })
</script>

<style scoped>
.click-outside-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
  position: relative;
}

.dropdown {
  color: black;
  position: absolute;
  top: 60px;
  background-color: white;
  border: 1px solid #ddd;
  border-radius: 4px;
  width: 200px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  z-index: 10;
}

.dropdown-header {
  padding: 10px;
  background-color: #f5f5f5;
  border-bottom: 1px solid #ddd;
  font-weight: bold;
  text-align: center;
}

.dropdown-content {
  padding: 10px 0;
}

.dropdown-item {
  padding: 8px 16px;
  cursor: pointer;
}

.dropdown-item:hover {
  background-color: #f5f5f5;
}

.click-count {
  padding: 10px;
  text-align: center;
  font-size: 0.9em;
  color: #666;
  border-top: 1px solid #ddd;
}
</style>
