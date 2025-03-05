<template>
  <div>
    <h3>Responsive Design Helper</h3>
    <div class="info-box">
      <p>Current breakpoint: {{ current }}</p>
      <p>Screen width: {{ width }}px</p>
      <div v-if="isSmallOnly">
        <p><strong>This will be only displayed if the window size is Small</strong></p>
      </div>
      <div v-if="isBetweenSmAndMd">
        <p><strong>This will be only displayed if the window size is between Small and Medium</strong></p>
      </div>
      <div v-if="isBetweenMdAndLg">
        <p><strong>This will be only displayed if the window size is between Medium and Large</strong></p>
      </div>
      <div v-if="isBetweenLgAndXl">
        <p><strong>This will be only displayed if the window size is between Large and Extra Large</strong></p>
      </div>
      <div v-if="isBetweenXlAnd2xl">
        <p><strong>This will be only displayed if the window size is between Extra Large and 2 Extra Large</strong></p>
      </div>
      <div v-if="is2xlOnly">
        <p><strong>This will be only displayed if the window size is larger 2 Extra Large</strong></p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { useBreakpoints, breakpointsTailwind, useWindowSize } from '@vueuse/core'

// Use Tailwind's default breakpoints or define your own
const breakpoints = useBreakpoints(breakpointsTailwind)
const { width } = useWindowSize()

// Check if the screen width is greater than or equal to a breakpoint
const isSmallOnly = breakpoints.smallerOrEqual('sm')
const isBetweenSmAndMd = breakpoints.between('sm', 'md')
const isBetweenMdAndLg = breakpoints.between('md','lg')
const isBetweenLgAndXl = breakpoints.between('lg','xl')
const isBetweenXlAnd2xl = breakpoints.between('xl','2xl')
const is2xlOnly = breakpoints.greaterOrEqual('2xl')

// Get current breakpoint name
const current = breakpoints.current()
</script>
