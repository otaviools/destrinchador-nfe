<script lang="ts" setup>
import { header } from '#build/ui'
import type { NavigationMenuItem } from '@nuxt/ui'
</script>

<template>
  <header
    class="sticky top-0 z-50 w-full border-b border-gray-200 bg-gray-50 backdrop-blur dark:border-gray-950 dark:bg-gray-700"
  >
    <UHeader
      :ui="{
        toggle: 'hidden',
        root: 'h-14 min-h-0 py-2'
      }"
    >
      <template #title>
        <p class="text-lg font-bold">destrinchador-NFe</p>
      </template>

      <template #right>
        <UColorModeButton />
        <UTooltip arrow :delay-duration="300" text="Repositório">
          <UButton
            color="neutral"
            variant="ghost"
            to="https://github.com/otaviools/destrinchador-nfe"
            target="_blank"
            icon="i-simple-icons-github"
            aria-label="GitHub"
          />
        </UTooltip>
      </template>
    </UHeader>
  </header>
</template>

<style></style>
