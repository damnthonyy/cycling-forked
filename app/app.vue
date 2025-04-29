<script setup lang="ts">
import Footer from '~/components/Footer.vue'

useHead({
  meta: [
    { name: 'viewport', content: 'width=device-width, initial-scale=1' }
  ],
  link: [
    { rel: 'icon', href: '/favicon.ico' }
  ],
  htmlAttrs: {
    lang: 'en'
  }
})

const title = 'Cycling'
const description = 'make cycling easier'

useSeoMeta({
  title,
  description,
  ogTitle: title,
  ogDescription: description,
  ogImage: 'https://avatars.githubusercontent.com/u/189975276?s=200&v=4',
  twitterImage: 'https://ui-pro-starter.nuxt.dev/social-card.png',
  twitterCard: 'summary_large_image'
})
</script>

<template>
  <UApp>
    <UHeader>
      <template #left>
        <NuxtLink to="/">
          <!-- <LogoPro class="w-auto h-6 shrink-0" /> -->
          <!-- <h1>Cycling</h1> -->
           <img src="https://avatars.githubusercontent.com/u/189975276?s=200&v=4" class="w-auto h-6 shrink-0 w-10 h-10 bg-gray-200" />
        </NuxtLink>

        <TemplateMenu />
      </template>

      <template #right>
        <UColorModeButton />

        <UButton
          to="https://github.com/your-username/cycling"
          target="_blank"
          icon="i-simple-icons-github"
          aria-label="GitHub"
          color="neutral"
          variant="ghost"
        />
      </template>
    </UHeader>

    <UMain>
      <NuxtPage />
    </UMain>

    <USeparator icon="i-simple-icons-nuxtdotjs" />
    
    <Footer />
  </UApp>
</template>
