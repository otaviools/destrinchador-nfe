<script setup lang="ts">
  const router = useRouter()
  const notaFiscal = ref('123456789012345678901234567890123456789012345')
</script>

<template>
  <div class="flex min-h-screen items-center justify-center p-4">

    <div class="w-full max-w-2xl flex items-center justify-center gap-4 mt-2">
      <UInput class="flex-1" v-model="notaFiscal" placeholder="Chave de acesso" size="xl"/>
      <UButton 
        icon="i-lucide-rocket" 
        size="xl" 
        class="max-w-none" 
        @click="router.push(`/${notaFiscal}`)">Consultar</UButton>
    </div>
  </div>
</template>
