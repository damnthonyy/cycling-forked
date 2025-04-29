<template>
  <nav class="w-64 p-4 bg-gray-50/80 backdrop-blur-sm fixed top-0 left-0 h-screen hidden md:block border-r border-gray-100 z-10">
    <ul class="space-y-2">
      <li v-for="sec in sections" :key="sec.id">
        <a 
          :href="`#${sec.id}`" 
          class="block px-4 py-2 text-gray-600 hover:text-primary-600 hover:bg-primary-50 rounded-lg transition-all duration-200"
          :class="{ 'text-primary-600 bg-primary-50': activeSection === sec.id }"
          @click="handleClick"
        >
          {{ sec.title }}
        </a>
      </li>
    </ul>
  </nav>
</template>

<script setup lang="ts">
import { sections } from '@/layout/privacy-policy';
import { ref, onMounted, onUnmounted } from 'vue';

const activeSection = ref('');

const handleClick = (e: MouseEvent) => {
  e.preventDefault();
  const target = e.target as HTMLAnchorElement;
  const id = target.getAttribute('href')?.replace('#', '');
  if (id) {
    const element = document.getElementById(id);
    if (element) {
      element.scrollIntoView({ behavior: 'smooth' });
      activeSection.value = id;
    }
  }
};
</script>
  