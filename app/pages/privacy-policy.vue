<template>
  <div class="flex">
    <PrivacySidebar />

    <main class="flex-1 p-8 md:ml-64">
      <h1 class="text-3xl font-bold mb-6">Politique de confidentialité</h1>

      <section
        v-for="sec in sections"
        :key="sec.id"
        :id="sec.id"
        class="mb-8 scroll-mt-20"
      >
        <h2 class="text-2xl font-semibold mb-2">{{ sec.title }}</h2>
        <p>{{ sec.text }}</p>
      </section>
    </main>
  </div>
</template>

<script setup lang="ts">
import PrivacySidebar from '@/components/PrivacySidebar.vue';
import { sections } from '@/layout/privacy-policy';
</script>

