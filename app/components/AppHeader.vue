<script lang="ts" setup>
import type { NavigationMenuItem } from '@nuxt/ui'
</script>

<template>
  <div>
    <UHeader>
      <template #title>
        <p class="text-lg font-bold">destrinchar NF-e</p>
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
  </div>
</template>

<style></style>
