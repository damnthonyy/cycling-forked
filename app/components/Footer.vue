<template>
  <UFooter>
    <UNavigationMenu :items="items" variant="link" />
  </UFooter>
</template>

<script setup lang="ts">
const items: NavigationMenuItem[] = [
  { label: 'About', to: '/about' },
  { label: 'Features', to: '/features' },
  { label: 'Privacy policy', to: '/privacy-policy' }
]
</script>
