<script setup lang="ts">
const isDark = usePreferredDark()

const title = 'destrinchar NF-e'
const description = 'O destrinchador'

useHead({
  meta: [{ name: 'viewport', content: 'width=device-width, initial-scale=1' }],
  htmlAttrs: {
    lang: 'pt-br'
  }
})

useSeoMeta({
  title,
  description,
  ogTitle: title,
  ogDescription: description
})
</script>

<template>
  <UApp>
    <AppHeader />
    <NuxtPage />
    <AppFooter />
  </UApp>
</template>
