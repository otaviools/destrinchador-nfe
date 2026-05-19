<script setup lang="ts">
  const route = useRoute()
  const nota = route.params.nota as string
</script>

<template>
    <div>
        <h1>Nota Fiscal Eletrônica</h1>
        <p>Chave de Acesso: {{ nota }}</p>
        <!-- Aqui você pode adicionar mais detalhes da NFe, como emitente, destinatário, itens, etc. -->
    </div>
</template>