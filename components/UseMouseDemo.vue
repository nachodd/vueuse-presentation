<template>
  <div>
    <p>
      Mouse Position Tracker: <span>X: {{ x }}, Y: {{ y }}</span>
      <br>
      <UMouse v-slot="{ x, y }"> UseMouse component: X: {{ x }}, Y: {{ y }}</UMouse>
    </p>
  </div>
</template>

<script setup>
import { useMouse } from '@vueuse/core'
import { UseMouse as UMouse } from "@vueuse/components"

// Destructure to get reactive x and y coordinates
const { x, y } = useMouse()
</script>
