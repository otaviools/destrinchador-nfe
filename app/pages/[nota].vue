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
  id: string
  titulo: string
  objeto: string
  valorObjeto: string
  resultadoHumanizado: string
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

const columns: TableColumn<LinhaMatriz>[] = [
  {
    accessorKey: 'titulo',
    header: 'Título',
    cell: ({ row }: RowCellContext) =>
      h('span', { class: 'font-semibold dark:text-gray-300 text-gray-900' }, row.getValue('titulo'))
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

const dataInvertida = ref<LinhaMatriz[]>([])

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

  dataInvertida.value = [
    {
      id: '1',
      titulo: 'Nota Fiscal',
      objeto: 'nNF',
      valorObjeto: numeroBruto,
      resultadoHumanizado: numeroFormatado
    },
    {
      id: '2',
      titulo: 'Série da NF-e',
      objeto: 'serie',
      valorObjeto: serieBruta,
      resultadoHumanizado: serieFormatada
    },
    {
      id: '3',
      titulo: 'Modelo da NF-e',
      objeto: 'mod',
      valorObjeto: modelo,
      resultadoHumanizado: modelo
    },
    {
      id: '4',
      titulo: 'CNPJ',
      objeto: 'CNPJ',
      valorObjeto: cnpjBruto,
      resultadoHumanizado: cnpjFormatado
    },
    {
      id: '5',
      titulo: 'Data Emissão',
      objeto: 'AAMM',
      valorObjeto: `${ano}${mes}`,
      resultadoHumanizado: dataEmissao
    },
    {
      id: '6',
      titulo: 'UF (Estado)',
      objeto: 'cUF',
      valorObjeto: codigoUF,
      resultadoHumanizado: ufSigla
    },
    {
      id: '7',
      titulo: 'Tipo de Emissão',
      objeto: 'tpEmis',
      valorObjeto: tipoEmissaoCodigo,
      resultadoHumanizado: tipoEmissaoTexto
    },
    {
      id: '8',
      titulo: 'Código Aleatório',
      objeto: 'cNF',
      valorObjeto: codigoAleatorio,
      resultadoHumanizado: codigoAleatorio
    },
    {
      id: '9',
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
  <div class="mx-auto flex w-full max-w-5xl flex-col gap-8 px-8 py-8 dark:text-white">
    <UTable
      v-if="dataInvertida.length > 0"
      :data="dataInvertida"
      :columns="columns"
      class="overflow-hidden rounded-xl border border-gray-200 bg-gray-100 text-gray-900 shadow-2xl transition-colors duration-300 dark:border-neutral-800 dark:bg-neutral-900 dark:text-neutral-200"
    />

    <UAlert
      v-else
      color="primary"
      variant="subtle"
      icon="i-lucide-shield-alert"
      description="A chave NF-e deve conter 44 números. Verifique se a chave foi digitada corretamente."
    />

    <UButton size="xl" color="primary" class="w-fit cursor-pointer" @click="$router.push('/')">
      Voltar
    </UButton>
  </div>
</template>
