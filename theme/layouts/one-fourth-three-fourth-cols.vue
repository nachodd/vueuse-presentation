<template>
  <div class="slidev-layout">
    <h1 v-if="$attrs.slideTitle">{{ $attrs.slideTitle }}</h1>
    <slot name="default" />
    <div class="grid grid-cols-4 gap-4">
      <div class="prose" :class="[$attrs.colConfig === '1:3' && 'col-span-3']">
        <slot name="left" />
      </div>
      <div class="prose" :class="[(!$attrs.colConfig || $attrs.colConfig === '3:1') && 'col-span-3']">
        <slot name="right" />
      </div>
    </div>
    <div v-if="$attrs.footer" v-html="$attrs.footer"></div>
  </div>
</template>
