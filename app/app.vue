<script setup lang="ts">
const isDark = usePreferredDark()

const favicon = computed(() => (isDark.value ? '/favicon-dark.ico' : '/favicon-light.ico'))

const title = 'destrinchar NF-e'
const description = 'O destrinchador'

useHead({
  meta: [{ name: 'viewport', content: 'width=device-width, initial-scale=1' }],
  link: [{ rel: 'icon', href: favicon }],
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
