<script setup lang="ts">
const route = useRoute()
const notaFiscal = computed(() => {
  return (route.params.nota || '') as string
})

const mapeamentoUF: Record<string, string> = {
  11: 'RO',
  12: 'AC',
  13: 'AM',
  14: 'RR',
  15: 'PA',
  16: 'AP',
  17: 'TO',
  21: 'MA',
  22: 'PI',
  23: 'CE',
  24: 'RN',
  25: 'PB',
  26: 'PE',
  27: 'AL',
  28: 'SE',
  29: 'BA',
  31: 'MG',
  32: 'ES',
  33: 'RJ',
  35: 'SP',
  41: 'PR',
  42: 'SC',
  43: 'RS',
  50: 'MS',
  51: 'MT',
  52: 'GO',
  53: 'DF'
}

const mapeamentoTpEmis: Record<string, string> = {
  1: 'Normal',
  2: 'Contingência FS-IA',
  3: 'Contingência SCAN',
  4: 'Contingência DPEC',
  5: 'Contingência FS-DA',
  6: 'Contingência SVC-AN',
  7: 'Contingência SVC-RS',
  9: 'Contingência off-line NFC-e'
}

type LinhaMatriz = {
  titulo: string
  objeto: string
  valorObjeto: string
  resultadoHumanizado: string
  [key: string]: string
}

type RowCellContext = {
  row: {
    getValue: (key: keyof LinhaMatriz) => string
  }
}

type TableColumn<T> = {
  accessorKey: keyof T
  header: string
  cell: (context: RowCellContext) => ReturnType<typeof h>
}

const columns: any[] = [
  {
    accessorKey: 'titulo',
    header: 'Título',
    cell: ({ row }: RowCellContext) =>
      h(
        'span',
        { class: 'font-semibold dark:text-gray-300 !text-blue-500' },
        row.getValue('titulo')
      )
  },
  {
    accessorKey: 'objeto',
    header: 'Objeto (XML)',
    cell: ({ row }: RowCellContext) =>
      h(
        'code',
        {
          class:
            'px-1.5 py-0.5 rounded bg-neutral dark:text-gray-400 text-gray-900 font-mono text-xs border border-gray-800'
        },
        row.getValue('objeto')
      )
  },
  {
    accessorKey: 'valorObjeto',
    header: 'Valor do Objeto (Bruto)',
    cell: ({ row }: RowCellContext) =>
      h(
        'span',
        { class: 'font-mono dark:text-gray-400 text-gray-500 text-sm' },
        row.getValue('valorObjeto')
      )
  },
  {
    accessorKey: 'resultadoHumanizado',
    header: 'Resultado Humanizado',
    cell: ({ row }: RowCellContext) =>
      h(
        'span',
        { class: 'font-medium text-orange-400 text-sm' },
        row.getValue('resultadoHumanizado')
      )
  }
]

const data = ref<LinhaMatriz[]>([])

const destrinchar = () => {
  const chave = notaFiscal.value.replace(/\D/g, '')
  if (chave.length !== 44) return

  const codigoUF = chave.substring(0, 2)
  const ufSigla = mapeamentoUF[codigoUF] || codigoUF
  const ano = chave.substring(2, 4)
  const mes = chave.substring(4, 6)
  const dataEmissao = `${mes}/20${ano}`
  const cnpjBruto = chave.substring(6, 20)
  const cnpjFormatado = cnpjBruto.replace(/^(\d{2})(\d{3})(\d{3})(\d{4})(\d{2})$/, '$1.$2.$3/$4-$5')
  const modelo = chave.substring(20, 22)
  const serieBruta = chave.substring(22, 25)
  const serieFormatada = Number(serieBruta).toString()
  const numeroBruto = chave.substring(25, 34)
  const numeroFormatado = Number(numeroBruto).toLocaleString('pt-BR')
  const tipoEmissaoCodigo = chave.substring(34, 35)
  const tipoEmissaoTexto = mapeamentoTpEmis[tipoEmissaoCodigo] || tipoEmissaoCodigo
  const codigoAleatorio = chave.substring(35, 43)
  const dv = chave.substring(43, 44)

  data.value = [
    {
      titulo: 'Nota Fiscal',
      objeto: 'nNF',
      valorObjeto: numeroBruto,
      resultadoHumanizado: numeroFormatado
    },
    {
      titulo: 'Série da NF-e',
      objeto: 'serie',
      valorObjeto: serieBruta,
      resultadoHumanizado: serieFormatada
    },
    {
      titulo: 'Modelo da NF-e',
      objeto: 'mod',
      valorObjeto: modelo,
      resultadoHumanizado: modelo
    },
    {
      titulo: 'CNPJ',
      objeto: 'CNPJ',
      valorObjeto: cnpjBruto,
      resultadoHumanizado: cnpjFormatado
    },
    {
      titulo: 'Data Emissão',
      objeto: 'AAMM',
      valorObjeto: `${ano}${mes}`,
      resultadoHumanizado: dataEmissao
    },
    {
      titulo: 'UF (Estado)',
      objeto: 'cUF',
      valorObjeto: codigoUF,
      resultadoHumanizado: ufSigla
    },
    {
      titulo: 'Tipo de Emissão',
      objeto: 'tpEmis',
      valorObjeto: tipoEmissaoCodigo,
      resultadoHumanizado: tipoEmissaoTexto
    },
    {
      titulo: 'Código Aleatório',
      objeto: 'cNF',
      valorObjeto: codigoAleatorio,
      resultadoHumanizado: codigoAleatorio
    },
    {
      titulo: 'Dígito Verificador',
      objeto: 'cDV',
      valorObjeto: dv,
      resultadoHumanizado: dv
    }
  ]
}

watchEffect(() => {
  if (notaFiscal.value && notaFiscal.value.replace(/\D/g, '').length === 44) {
    destrinchar()
  }
})
</script>

<template>
  <main
    class="mx-auto flex w-full max-w-5xl flex-col gap-6 px-4 py-6 md:gap-8 md:px-8 md:py-8 dark:text-white"
  >
    <section v-if="data.length > 0" class="w-full">
        <UTable :data="data" :ui="{ separator: 'hidden', th: 'py-1 text-primary' }" />
      </UCard>
    </section>

    <UAlert
      v-else
      color="primary"
      variant="subtle"
      icon="i-lucide-shield-alert"
      description="A chave NF-e deve conter 44 números. Verifique se a chave foi digitada corretamente."
      class="w-full"
    />

    <UButton
      size="xl"
      color="primary"
      class="w-full cursor-pointer justify-center sm:w-fit"
      @click="$router.push('/')"
    >
      Voltar
    </UButton>
  </main>
</template>
