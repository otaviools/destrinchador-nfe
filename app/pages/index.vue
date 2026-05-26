<script setup lang="ts">
const notaFiscal = ref('43230707385111000102555030007659731240866593')
const router = useRouter()

const validacao = computed(() => {
  const valor = notaFiscal.value.trim()

  // 1. Verificar se contém apenas números
  if (valor && !/^\d+$/.test(valor)) {
    return {
      valido: false,
      mensagem: 'A chave NF-e deve conter apenas números.',
      icone: 'i-lucide-shield-alert',
      cor: 'error' as const
    }
  }

  // 2. Menor que 44
  if (valor.length < 44) {
    return {
      valido: false,
      mensagem: `Chave incompleta. Faltam ${44 - valor.length} dígitos (digitados: ${valor.length}/44).`,
      icone: 'i-lucide-shield-alert',
      cor: 'warning' as const
    }
  }

  // 3. Maior que 44
  if (valor.length > 44) {
    return {
      valido: false,
      mensagem: `Chave excedente. Excesso de ${valor.length - 44} dígitos (digitados: ${valor.length}/44).`,
      icone: 'i-lucide-shield-alert',
      cor: 'error' as const
    }
  }

  // 4. Chave Valida
  return {
    valido: true,
    mensagem: 'Chave NF-e válida, pronta para consulta.',
    icone: 'i-lucide-check',
    cor: 'success' as const
  }
})
const consultarNota = () => {
  if (validacao.value.valido) {
    router.push(`/${notaFiscal.value.trim()}`)
  }
}
</script>

<template>
  <div
    class="mx-auto flex min-h-screen w-full max-w-2xl flex-col items-start justify-center gap-6 px-4 py-8 text-gray-900 dark:text-white"
  >
    <h1 class="font-titulo text-3xl font-medium text-gray-900 sm:text-5xl dark:text-white">
      destrinchador de NF-e
    </h1>

    <UAlert
      class="w-full"
      :color="validacao.cor"
      variant="subtle"
      :icon="validacao.icone"
      :description="validacao.mensagem"
    />

    <div class="flex w-full flex-col items-stretch gap-4 sm:flex-row sm:items-center">
      <UInput v-model="notaFiscal" class="w-full flex-1" size="xl" placeholder="Chave NF-e" />

      <UButton
        class="cursor-pointer justify-center"
        :disabled="!validacao.valido"
        size="xl"
        @click="consultarNota"
      >
        Consultar
      </UButton>
    </div>
  </div>
</template>
