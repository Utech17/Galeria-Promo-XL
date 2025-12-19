<template>
  <div>
    <SpeedInsights />
    <slot />
  </div>
</template>

<script setup>
import { SpeedInsights } from '@vercel/speed-insights/vue'
</script>
