<script setup lang="ts">
const notaFiscal = ref("43230707385111000102555030007659731240866593");
const router = useRouter();
const consultarNota = () => {
  router.push(`/${notaFiscal.value}`);
};
</script>
<template>
  <div
    class="w-full max-w-2xl min-h-screen px-4 py-8 mx-auto flex flex-col items-start justify-center gap-6"
  >
    <h1 class="font-titulo text-5xl font-bold text-white">
      Destrinchador de NF-e
    </h1>

    <UAlert
    v-if="!notaFiscal || notaFiscal.length !== 44"
    color="primary"
    variant="subtle"
    icon="i-lucide-shield-alert"
    description="A chave NF-e deve conter 44 caracteres."
    />

    <div class="flex w-full items-center gap-4">
      <UInput
        class="flex-1"
        size="xl"
        v-model="notaFiscal"
        placeholder="Chave NF-e"
      />

      <UButton
        class="cursor-pointer"
        :disabled="!notaFiscal || notaFiscal.length !== 44 || !/^\d+$/.test(notaFiscal)"
        @click="consultarNota"
        size="xl"
        >Consultar</UButton
      >
    </div>
  </div>
</template>
