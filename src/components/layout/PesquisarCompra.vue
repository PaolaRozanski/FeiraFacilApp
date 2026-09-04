<script setup>
import { ref, computed} from 'vue'
import { useCompraStore } from '@/stores/compra'

const compraStore = useCompraStore()
const filtro = ref('')

const tarefasFiltradas = computed(() => {
  const termo = filtro.value.trim().toLowerCase()
  if (!termo) return compraStore.compras
  return compraStore.compras.filter(t => 
    t.desc.toLowerCase().includes(termo) || 
    t.nome.toLowerCase().includes(termo)
  )
})

const limparFiltro = () => {
  filtro.value = ''
}
</script>

<template>
  <div class="filtro-container">
    <div class="filtro-input-group">
      <input
        v-model="filtro"
        type="text"
        placeholder="Pesquisar produto..."
        class="filtro-input"
      />
      <button v-if="filtro" @click="limparFiltro" class="btn-limpar">
        ✕
      </button>
    </div>
  </div>
  
  <ul class="compras-list">
    <taskChild
      v-for="tarefa in tarefasFiltradas"
      :key="tarefa.id"
      :id="tarefa.id"
      :descricao="tarefa.desc"
      :status="tarefa.status"
      @excluir="deleteTarefa"
      @editar="editTarefa"
      @alternar="alternarStatus"
    />
  </ul>  
</template>

<style scoped>

</style>