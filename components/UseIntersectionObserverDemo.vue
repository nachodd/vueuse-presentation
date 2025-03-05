<template>
  <div class="container">
    <div class="items-container">
      <div v-for="item in visibleItems" :key="item.id" class="item">
        {{ item.name }}
      </div>
    </div>
    <!-- Observer target -->
    <div ref="loadMoreTrigger" class="loading-trigger">
      <span v-if="isLoading">Loading more items...</span>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { useIntersectionObserver } from '@vueuse/core'

const allItems = ref([...Array(100)].map((_, i) => ({ id: i, name: `Item ${i}` })))
const loadedCount = ref(20)
const isLoading = ref(false)
const loadMoreTrigger = ref(null)

const visibleItems = computed(() => allItems.value.slice(0, loadedCount.value))

// When trigger element is visible, load more items
useIntersectionObserver(loadMoreTrigger, ([{ isIntersecting }]) => {
  if (isIntersecting && loadedCount.value < allItems.value.length) {
    loadMoreItems()
  }
})

function loadMoreItems() {
  isLoading.value = true
  // Simulate API delay
  setTimeout(() => {
    loadedCount.value += 10
    isLoading.value = false
  }, 500)
}
</script>
<style>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
  border: 1px solid #ccc;
  max-height: calc(100vh - 420px);
  overflow-y: auto;
}
.loading-trigger {
  margin-bottom: 10px;
}
</style>
