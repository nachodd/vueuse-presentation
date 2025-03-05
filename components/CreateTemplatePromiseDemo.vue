<template>
  <div class="file-upload-container">
    <div class="upload-wrapper">
      <button @click="openFileInput" class="upload-button">
        {{ file ? 'Change Image' : 'Select Image' }}
      </button>
    </div>

    <div v-if="file" class="preview-container">
      <img :src="previewUrl" alt="Preview" class="preview-image" />
      <div class="file-info">
        <p>File name: {{ file.name }}</p>
        <p>Size: {{ (file.size / 1024).toFixed(2) }} KB</p>
      </div>
    </div>

    <FileInputPromise v-slot="{ resolve, reject, args: [numberOfTimesOpened] }">
      <div class="modal-overlay">
        <div class="modal-content">
          <h2 class="modal-title">Upload Image</h2>
          <p class="modal-text">Number of times opened: {{ numberOfTimesOpened }}</p>
          <input
            type="file"
            accept="image/*"
            @change="(e) => resolve(e.target.files[0])"
            class="file-input"
          />
          <button
            class="cancel-button"
            @click="reject"
          >
            Cancel
          </button>
        </div>
      </div>
    </FileInputPromise>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { createTemplatePromise } from '@vueuse/core'

const file = ref(null)
const previewUrl = ref('')
const numberOfTimesOpened = ref(0)

// Create a template promise component for file input
const FileInputPromise = createTemplatePromise({
  transition: {
    name: 'fade',
    appear: true,
  },
})

// Function to trigger file input
async function openFileInput() {
  try {
    numberOfTimesOpened.value++
    const result = await FileInputPromise.start(numberOfTimesOpened)
    if (result) {
      file.value = result
      previewUrl.value = URL.createObjectURL(result)
    }
  } catch (e) {
    file.value = null
    previewUrl.value = ''
  }
}
</script>

<style scoped>
.file-upload-container {
  @apply flex flex-col items-center gap-5 p-5 max-w-md mx-auto;
}

.upload-wrapper {
  @apply w-full text-center;
}

.upload-button {
  @apply px-2 py-2 bg-green-500 text-white border-none rounded cursor-pointer text-base;
  @apply transition-colors duration-300 ease-in-out;
  @apply hover:bg-green-600;
}

.preview-container {
  @apply w-full text-center;
}

.preview-image {
  @apply max-w-full max-h-[300px] rounded shadow-md;
}

.file-info {
  @apply mt-2.5 text-gray-600 text-sm;
}

.modal-overlay {
  @apply fixed inset-0 bg-black/50 flex items-center justify-center z-50;
}

.modal-content {
  @apply bg-white text-black p-8 rounded-lg flex flex-col gap-4;
}

.modal-title {
  @apply text-xl font-semibold;
}

.modal-text {
  @apply text-gray-600;
}

.file-input {
  @apply block mb-4 w-full;
  @apply file:mr-4 file:py-2 file:px-4;
  @apply file:rounded-md file:border-0;
  @apply file:text-sm file:font-semibold;
  @apply file:bg-green-50 file:text-green-700;
  @apply hover:file:bg-green-100;
}

.cancel-button {
  @apply px-4 py-2 bg-red-600 text-white border-none rounded cursor-pointer text-sm;
  @apply transition-colors duration-300 ease-in-out;
  @apply hover:bg-red-700;
}

/* Transition classes */
.fade-enter-active,
.fade-leave-active {
  @apply transition-opacity duration-500;
}

.fade-enter,
.fade-leave-to {
  @apply opacity-0;
}
</style>
