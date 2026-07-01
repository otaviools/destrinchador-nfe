<script setup lang="ts">
const route = useRoute()
const router = useRouter()
const notaFiscal = computed(() => (route.params.nota || '') as string)
const notaFiscalInput = ref((route.params.nota || '') as string)
import type { BreadcrumbItem } from '@nuxt/ui'

watch(
  () => route.params.nota,
  (novaNota) => {
    notaFiscalInput.value = (novaNota || '') as string
  }
)

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
  Título: string
  XML: string
  Valor: string
  Resultado: string
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
    accessorKey: 'Título',
    header: 'Título',
    cell: ({ row }: RowCellContext) =>
      h('span', { class: 'font-normal text-black dark:text-gray-300' }, row.getValue('Título'))
  },
  {
    accessorKey: 'XML',
    header: 'XML',
    cell: ({ row }: RowCellContext) =>
      h(
        'code',
        {
          class:
            'px-1.5 py-0.5 rounded text-primary dark:border-neutral-700 border border-gray-400 font-mono text-xs '
        },
        row.getValue('XML')
      )
  },
  {
    accessorKey: 'Valor',
    header: 'Valor',
    cell: ({ row }: RowCellContext) =>
      h(
        'span',
        { class: 'font-mono dark:text-gray-400 text-gray-500 text-sm' },
        row.getValue('Valor')
      )
  },
  {
    accessorKey: 'Resultado',
    header: 'Resultado',
    cell: ({ row }: RowCellContext) =>
      h('span', { class: 'font-normal text-black dark:text-white ' }, row.getValue('Resultado'))
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
      Título: 'Nota Fiscal',
      XML: 'nNF',
      Valor: numeroBruto,
      Resultado: numeroFormatado
    },
    {
      Título: 'Série da NF-e',
      XML: 'serie',
      Valor: serieBruta,
      Resultado: serieFormatada
    },
    {
      Título: 'Modelo da NF-e',
      XML: 'mod',
      Valor: modelo,
      Resultado: modelo
    },
    {
      Título: 'CNPJ',
      XML: 'CNPJ',
      Valor: cnpjBruto,
      Resultado: cnpjFormatado
    },
    {
      Título: 'Data Emissão',
      XML: 'AAMM',
      Valor: `${ano}${mes}`,
      Resultado: dataEmissao
    },
    {
      Título: 'UF (Estado)',
      XML: 'cUF',
      Valor: codigoUF,
      Resultado: ufSigla
    },
    {
      Título: 'Tipo de Emissão',
      XML: 'tpEmis',
      Valor: tipoEmissaoCodigo,
      Resultado: tipoEmissaoTexto
    },
    {
      Título: 'Código Aleatório',
      XML: 'cNF',
      Valor: codigoAleatorio,
      Resultado: codigoAleatorio
    },
    {
      Título: 'Dígito Verificador',
      XML: 'cDV',
      Valor: dv,
      Resultado: dv
    }
  ]
}

watch(
  () => notaFiscal.value,
  (novaNota) => {
    if (novaNota && novaNota.replace(/\D/g, '').length === 44) {
      destrinchar()
    } else {
      data.value = []
    }
  },
  { immediate: true }
)

const validacaoInput = computed(() => {
  const valor = notaFiscalInput.value.trim()

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
  if (validacaoInput.value.valido) {
    router.push(`/${notaFiscalInput.value.trim()}`)
  }
}
const items = ref<BreadcrumbItem[]>([
  {
    label: 'Voltar',
    icon: 'lucide-step-back',
    to: '/'
  },
  {
    label: 'Nota',
    icon: 'lucide-notebook-text',
    to: ''
  }
])
</script>

<template>
  <main
    class="mx-auto flex w-full max-w-5xl flex-col gap-6 px-4 py-6 md:gap-8 md:px-8 md:py-8 dark:text-white"
  >
    <section v-if="data.length > 0" class="w-full">
      <UCard class="shadow-xs">
        <template #header>
          <div class="flex items-center justify-between gap-2 text-xs md:text-base">
            <UBreadcrumb
              :ui="{ linkLeadingIcon: 'size-4', separatorIcon: 'size-3.5' }"
              class="hidden md:flex"
              :items="items"
            />
            <div class="hidden items-center gap-2 md:flex">
              <UInput
                v-model="notaFiscalInput"
                class="w-100"
                size="md"
                placeholder="Destrinchar outra NFe"
                maxlength="44"
                onkeypress="return event.charCode >= 48 && event.charCode <= 57"
                @input="notaFiscalInput = notaFiscalInput.replace(/\D/g, '')"
              />
              <UButton
                class="cursor-pointer justify-center"
                :disabled="!validacaoInput.valido"
                size="md"
                @click="consultarNota"
              >
                Consultar
              </UButton>
            </div>
          </div>
        </template>
        <UTable
          :data="data"
          :columns="columns"
          :ui="{ separator: 'hidden', th: 'py-1 text-primary', td: 'py-2' }"
        />
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
  </main>
</template>
