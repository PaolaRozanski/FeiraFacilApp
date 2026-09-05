<script setup>
import { computed, ref } from 'vue'
import { pedidos } from '@/data/pedidos'

const filtro = ref('')

const pedidosFiltrados = computed(() => {
  const termo = filtro.value.trim().toLowerCase()

  if (!termo) {
    return pedidos.value
  }

  return pedidos.value.filter((pedido) => {
    const codigo = pedido.codigo.toLowerCase()
    const cliente = pedido.cliente.toLowerCase()

    return codigo.includes(termo) || cliente.includes(termo)
  })
})

const totalPedidos = computed(() => pedidos.value.length)

const totalItensVendidos = computed(() => {
  return pedidos.value.reduce((total, pedido) => {
    return total + pedido.itens.reduce((subtotal, item) => {
      return subtotal + item.quantidade
    }, 0)
  }, 0)
})

const totalVendido = computed(() => {
  return pedidos.value.reduce((total, pedido) => {
    return total + pedido.itens.reduce((subtotal, item) => {
      return subtotal + item.precoUnitario * item.quantidade
    }, 0)
  }, 0)
})

const produtosDiferentes = (pedido) => {
  return pedido.itens.length
}

const totalItensPedido = (pedido) => {
  return pedido.itens.reduce((total, item) => {
    return total + item.quantidade
  }, 0)
}

const totalPedido = (pedido) => {
  return pedido.itens.reduce((total, item) => {
    return total + item.precoUnitario * item.quantidade
  }, 0)
}

const formatarMoeda = (valor) => {
  return new Intl.NumberFormat('pt-BR', {
    style: 'currency',
    currency: 'BRL',
  }).format(valor)
}
</script>

<template>
  <main class="container page">
    <header class="page-header">
      <h1>Resumo dos pedidos</h1>
      <p>
        Consulte os pedidos finalizados e o total vendido.
      </p>
    </header>

    <section
      class="summary-grid"
      aria-label="Resumo geral das vendas"
    >
      <article class="summary-card">
        <span>Pedidos realizados</span>
        <strong>{{ totalPedidos }}</strong>
      </article>

      <article class="summary-card">
        <span>Itens vendidos</span>
        <strong>{{ totalItensVendidos }}</strong>
      </article>

      <article class="summary-card">
        <span>Total vendido</span>
        <strong>{{ formatarMoeda(totalVendido) }}</strong>
      </article>
    </section>

    <section class="card" aria-labelledby="filtro-pedidos">
      <h2 id="filtro-pedidos">Filtrar pedidos</h2>

      <div class="filter-container">
        <div class="form-group">
          <label for="filtro">
            Nome do cliente ou código do pedido
          </label>

          <input
            id="filtro"
            v-model="filtro"
            name="filtro"
            type="search"
            placeholder="Digite o cliente ou código"
          />
        </div>

        <button class="button button-primary" type="button">
          Filtrar
        </button>
      </div>
    </section>

    <section class="card" aria-labelledby="pedidos-realizados">
      <h2 id="pedidos-realizados">Pedidos realizados</h2>

      <p v-if="pedidosFiltrados.length === 0" class="empty-state">
        Nenhum pedido encontrado.
      </p>

      <div v-else class="table-responsive">
        <table>
          <thead>
            <tr>
              <th scope="col">Código</th>
              <th scope="col">Cliente</th>
              <th scope="col">Produtos</th>
              <th scope="col">Itens</th>
              <th scope="col">Total</th>
            </tr>
          </thead>

          <tbody>
            <tr
              v-for="pedido in pedidosFiltrados"
              :key="pedido.codigo"
            >
              <td>{{ pedido.codigo }}</td>
              <td>{{ pedido.cliente }}</td>
              <td>{{ produtosDiferentes(pedido) }}</td>
              <td>{{ totalItensPedido(pedido) }}</td>
              <td>{{ formatarMoeda(totalPedido(pedido)) }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </section>
  </main>
</template>
