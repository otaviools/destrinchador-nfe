<script setup lang="ts">
import { main } from '#build/ui'

const notaFiscal = ref('43230707385111000102555030007659731240866593')
const router = useRouter()

const validacao = computed(() => {
  const valor = notaFiscal.value.trim()

  if (valor.length < 44) {
    return {
      valido: false,
      mensagem: `Chave incompleta. Digitados: ${valor.length}/44.`,
      icone: 'i-lucide-shield-alert',
      cor: 'warning' as const
    }
  }

  return {
    valido: true,
    mensagem: 'Chave NF-e válida.',
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
  <main
    class="mx-auto flex min-h-screen w-full max-w-2xl flex-col items-start justify-center gap-6 px-4 py-8 text-gray-900 dark:text-white"
  >
    <UAlert
      class="w-full"
      :color="validacao.cor"
      variant="subtle"
      :icon="validacao.icone"
      :description="validacao.mensagem"
    />

    <section class="flex w-full flex-col items-stretch gap-4 sm:flex-row sm:items-center">
      <UInput
        v-model="notaFiscal"
        class="w-full flex-1"
        size="xl"
        placeholder="Cole aqui sua chave Nfe"
        maxlength="44"
        onkeypress="return event.charCode >= 48 && event.charCode <= 57"
        @input="notaFiscal = notaFiscal.replace(/\D/g, '')"
      />

      <UButton
        class="cursor-pointer justify-center"
        :disabled="!validacao.valido"
        size="xl"
        @click="consultarNota"
      >
        Consultar
      </UButton>
    </section>
  </main>
</template>
