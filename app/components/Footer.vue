<template>
  <UFooter>
    <UNavigationMenu :items="items" variant="link" />
  </UFooter>
</template>

<script setup lang="ts">
const items = [
  { label: 'About', to: '/about' },
  { label: 'Features', to: '/features' },
  { label: 'Privacy policy', to: '/privacy-policy' }
] as const
</script>
