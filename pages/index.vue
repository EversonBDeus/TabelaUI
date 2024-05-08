<template>
  <div>
    <!-- Card -->
    <UCard>
      
      <!-- Header -->
      <template #header>
        <Placeholder class="h-8" />
        <h1>Registro de BA's</h1>
      </template>

      <Placeholder class="h-32 w-full" />

     
        <!-- Select Columns -->
        <div class="w-full h-10 flex justify-between items-center px-2.5 py-2.5 border-b border-gray-200 dark:border-gray-700">
          <UTooltip text="Linhas por página" :popper="{ placement: 'right' }">
            <USelectMenu v-model="selectedColumns" :options="columns" multiple placeholder="Columns" icon="i-heroicons-view-columns" />
          </UTooltip>
         
            <!-- Add Button -->
            <UTooltip text="Atalho" :shortcuts="['Ctrl', 'K']" :popper="{ placement: 'right' }">
              <UButton
                label="Adicionar"
                @click="isOpen = true"
                icon="i-heroicons-pencil-square"
                size="sm"
                color="primary"
                variant="solid"
                :trailing="false"
              />
            </UTooltip>



                              <!-- Search Input -->
                  <div class="flex px-3 py-3.5 border-gray-200 dark:border-gray-700">
                    <UInput v-model="q" placeholder="Buscar..." />
                  </div>
        </div>

                                <!-- Modal -->
                                <UModal v-model="isOpen" prevent-close>
              <UCard :ui="{ ring: '', divide: 'divide-y divide-gray-100 dark:divide-gray-800' }">
                <template #header>
                  <div class="flex items-center justify-between">
                    <h3 class="text-base font-semibold leading-6 text-gray-900 dark:text-white">
                      Adicionar novo BA
                    </h3>
                    <UButton color="gray" variant="ghost" icon="i-heroicons-x-mark-20-solid" class="-my-1" @click="isOpen = false" />
                  </div>
                </template>

                <Placeholder class="h-35 " />
                <!-- Form Group -->
                <UFormGroup type="text" v-slot="{ error }" :error="!name && 'Informe um nome'" help="Este é um nome">
                  <UInput :trailing-icon="error ? 'i-heroicons-exclamation-triangle-20-solid' : undefined" />
                </UFormGroup>
                <!-- Badge -->
                <UBadge size="sm" icon="i-heroicons-calendar-days-20-solid" :label="format(date, 'd MMM, yyy')"></UBadge>
              </UCard>
            </UModal>

      <!-- Table -->
      <UTable
      @select="select"
        v-model="selected"
        :columns="selectedColumns"
        :rows="filteredRows"
       
       
        sort-asc-icon="i-heroicons-arrow-up-20-solid"
        sort-desc-icon="i-heroicons-arrow-down-20-solid"
        :sort-button="    { icon: 'i-heroicons-sparkles-20-solid', color: 'primary', variant: 'outline', size: '2xs', square: false, ui: { w: 'w-15',  } }"
        class="w-full"
      >
        <template #name-data="{ row }">
          <span :class="[selected.find(person => person.id === row.id) && 'text-primary-500 dark:text-primary-400']">{{ row.name }}</span>
          
        </template>
        
        <template #actions-data="{ row }">
          <UDropdown :items="items(row)">
            <UButton color="gray" variant="ghost" icon="i-heroicons-ellipsis-horizontal-20-solid" />
          </UDropdown>
        </template>
        <template #date-data="{row}">
          <UBadge size="sm" icon="i-heroicons-calendar-days-20-solid" :label="format(date, 'd MMM, yyy')"></UBadge>
        </template>
        <template #status-data="{ row }">
          <UPopover mode="hover">
        <UBadge class="px-2 py-2" @update:modelValue="toggleRow(row)"  trailing-icon="i-heroicons-chevron-down-20-solid" size="xs" :label="row.toggleSelected ? 'Aberto' : 'Encerrado'" :color="row.toggleSelected ? 'emerald' : 'orange'"  variant="subtle" />
        <template #panel>
      <div class="p-4">
        <Placeholder class="h-20 w-48" />
        <UToggle
    on-icon="i-heroicons-check-20-solid"
    off-icon="i-heroicons-x-mark-20-solid"
    :style="{ backgroundColor: row.toggleSelected ? 'emerald' : 'orange' }"
    
    :modelValue="row.toggleSelected"
    
        @update:modelValue="row.toggleSelected = !row.toggleSelected"
   
  />
      </div>
    </template>
          </UPopover>
      
          
        </template>

        <template #icon-data="{ row }" >
  <UButton class="h-8 w-8 flex justify-center items-center text-xl py-1 px-1 font-semibold"      size="2xs"
          color="emerald"
          variant="outline"
          :ui="{ rounded: 'rounded-full' }"
          square
          
  
  >E</UButton>
</template>


      </UTable>
  
      <!-- Footer -->
      <template #footer>
        <Placeholder class="h-8" />
        <div class="flex justify-end">
          <UPagination v-model="page" :page-count="pageCount" :total="bas.length" />
        </div>
      </template>
    </UCard>
  </div>
</template>

<script setup>
import { format } from 'date-fns'
const name = ref('')
const date = ref(new Date())
const isOpen = ref(false)
const toggleSelected = ref(true)

// Definir atalhos
defineShortcuts({
  escape: {
    usingInput: true,
    whenever: [isOpen],
    handler: () => { isOpen.value = false }
  }
})



// Opções para num
const numOptions = [
  { label: 'Aberto', value: 'Aberto' },
  { label: 'Encerrado', value: 'Encerrado' }
];

// Itens de ação
const items = (row) => [
  [{
    label: 'Edit',
    icon: 'i-heroicons-pencil-square-20-solid',
    click: () => console.log('Edit', row.id)
  }, {
    label: 'Delete',
    icon: 'i-heroicons-trash-20-solid'
  }],
  [{
    label: 'Archive',
    icon: 'i-heroicons-archive-box-20-solid'
  }, {
    label: 'Máscara',
    icon: 'i-heroicons-clipboard-document-check'
  }],
  
]

// Colunas da tabela
const columns = [{
  key: 'id',
  role: 'Id',
  sortable: true,
  label: 'ID'
 
}, {
  key: 'icon',
  role: 'icon',
  sortable: true,
  label: 'Icon'
}, {
  key: 'name',
  role: 'name',
  sortable: true,
  label: 'Nome'
}, {
  key: 'uf',
  role: 'uf',
  sortable: true,
  label: 'UF'
}, {
  key: 'central',
  role: 'central',
  sortable: true,
  label: 'Central'
}, {
  key: 'num',
  role: 'num',
  sortable: true,
  label: 'Número'
}, {
  key: 'status',
  role: 'status',
  sortable: true,
  label: 'Status'
}, {
  key: 'date',
  role: 'date',
  sortable: true,
  label: 'Data'
}, {
  key: 'role',
  label: 'Actions'
}, {
  key: 'actions'
}]

// Colunas selecionadas
const selectedColumns = ref([...columns])


// BAs
const bas = [{
  id: 1,
  icon: 'Lindsay Walton',
  name: 'Everson Deus',
  uf: 'RO',
  central: '421',
  num: '34243242',
  status: 'Aberto',
  date: '01/05/2024',
  toggleSelected: false,
 
}, 
{
  id: 2,
  icon: 'Rindsay Walton',
  name: 'Dverson Deus',
  uf: 'SO',
  central: '321',
  num: '54243242',
  status: 'Encerrado',
  date: '02/05/2024',
  toggleSelected: false,
  
}]


// Filtro de linhas
const q = ref('')
const filteredRows = computed(() => {
  if (!q.value) {
    return bas
  }
  return bas.filter((bas) => {
    return Object.values(bas).some((value) => {
      return String(value).toLowerCase().includes(q.value.toLowerCase())
    })
  })
})

// Paginação
const page = ref(1)
const pageCount = 5
const rows = computed(() => {
  return bas.slice((page.value - 1) * pageCount, (page.value) * pageCount)
})
function select (row) {
  const index = selected.value.findIndex((item) => item.id === row.id)
  if (index === -1) {
    selected.value.push(row)
  } else {
    selected.value.splice(index, 1)
  }
}

// Selecionado
const selected = ref([bas[1]])
</script>

<style scoped>
/* Estilos personalizados aqui */

/* Ajustar a largura das colunas */

/* Ajustar o espaçamento entre as colunas */


</style>
