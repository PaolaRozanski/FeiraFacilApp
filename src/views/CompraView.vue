<script setup>
  import { ref } from 'vue'
  import { pedidos } from '@/data/pedidos'

  const codigoPedido = ref('')
  const nomeCliente = ref('')
  const nomeProduto = ref('')
  const precoUnitario = ref('')
  const quantidade = ref('')
  const produtos = ref([])
  const erroValidacao = ref('')

  const validarPedido = () => {
    if (!codigoPedido.value.trim() || !nomeCliente.value.trim()) {
      erroValidacao.value = 'Código do pedido e nome do cliente são obrigatórios'
      return false
    }
    erroValidacao.value = ''
    return true
  }

  const adicionarProduto = () => {
    if (!nomeProduto.value.trim() || !precoUnitario.value || !quantidade.value) {
      return
    }

    produtos.value.push({
      id: Date.now(),
      nome: nomeProduto.value,
      preco: parseFloat(precoUnitario.value),
      quantidade: parseInt(quantidade.value)
    })

    nomeProduto.value = ''
    precoUnitario.value = ''
    quantidade.value = ''
  }

  const removerProduto = (id) => {
    produtos.value = produtos.value.filter(p => p.id !== id)
  }

  const calcularTotal = () => {
    return produtos.value.reduce((total, p) => total + (p.preco * p.quantidade), 0)
  }

  const limpar = () => {
    codigoPedido.value = ''
    nomeCliente.value = ''
    produtos.value = []
  }

const finalizarPedido = () => {
  if (validarPedido() && produtos.value.length > 0) {
      pedidos.value.push({
      codigo: codigoPedido.value.trim(),
      cliente: nomeCliente.value.trim(),
      itens: produtos.value.map((produto) => ({
        id: produto.id,
        produto: produto.nome,
        precoUnitario: produto.preco,
        quantidade: produto.quantidade
      }))
    })

    limpar()
  }
}
</script>

<template>
  <main class="container page">
    <header class="page-header">
      <h1>Fazer compra</h1>
      <p>
        Cadastre o cliente e adicione os produtos do pedido.
      </p>
    </header>

    <section class="card" aria-labelledby="dados-pedido">
      <h2 id="dados-pedido">Dados do pedido</h2>

      <div class="form-grid form-grid-two-columns">
        <div class="form-group">
          <label for="codigoPedido">
            Código do pedido
          </label>

          <input
            id="codigoPedido"
            v-model="codigoPedido"
            name="codigoPedido"
            type="text"
            placeholder="Ex.: PED-001"
          />
        </div>

        <div class="form-group">
          <label for="nomeCliente">
            Nome do cliente
          </label>

          <input
            id="nomeCliente"
            v-model="nomeCliente"
            name="nomeCliente"
            type="text"
            placeholder="Digite o nome do cliente"
          />
        </div>
      </div>

      <div v-if="erroValidacao" class="error-message">
        {{ erroValidacao }}
      </div>
    </section>

    <section class="card" aria-labelledby="adicionar-produto">
      <h2 id="adicionar-produto">Adicionar produto</h2>

      <div class="form-grid form-grid-product">
        <div class="form-group">
          <label for="nomeProduto">
            Produto
          </label>

          <input
            id="nomeProduto"
            v-model="nomeProduto"
            name="nomeProduto"
            type="text"
            placeholder="Ex.: Tomate"
          />
        </div>

        <div class="form-group">
          <label for="precoUnitario">
            Preço unitário
          </label>

          <input
            id="precoUnitario"
            v-model="precoUnitario"
            name="precoUnitario"
            type="number"
            min="0"
            step="0.01"
            placeholder="0,00"
          />
        </div>

        <div class="form-group">
          <label for="quantidade">
            Quantidade
          </label>

          <input
            id="quantidade"
            v-model="quantidade"
            name="quantidade"
            type="number"
            min="1"
            step="1"
            placeholder="0"
          />
        </div>
      </div>

      <div class="form-actions">
        <button class="button button-primary" type="button" @click="adicionarProduto">
          Adicionar produto
        </button>
      </div>
    </section>

    <section class="card" aria-labelledby="itens-pedido">
      <h2 id="itens-pedido">Itens do pedido</h2>

      <div v-if="produtos.length === 0" class="empty-message">
        Nenhum produto foi adicionado ao pedido.
      </div>
      <div class="table-responsive">
        <table>
          <thead>
            <tr>
              <th scope="col">Produto</th>
              <th scope="col">Preço unitário</th>
              <th scope="col">Quantidade</th>
              <th scope="col">Total</th>
              <th scope="col">Ação</th>
            </tr>
          </thead>

          <tbody>
            <tr v-for="produto in produtos" :key="produto.id">
              <td>{{ produto.nome }}</td>
              <td>R$ {{ produto.preco.toFixed(2) }}</td>
              <td>{{ produto.quantidade }}</td>
              <td>R$ {{ (produto.preco * produto.quantidade).toFixed(2) }}</td>
              <td>
                <button type="button" @click="removerProduto(produto.id)">Excluir</button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <div class="order-total">
        <span>Total da compra</span>

        <strong>R$ {{ calcularTotal().toFixed(2) }}</strong>
      </div>

      <div class="form-actions">
        <button class="button button-secondary" type="button" @click="limpar">
          Limpar
        </button>

        <button class="button button-primary" type="button" @click="finalizarPedido">
          Finalizar pedido
        </button>
      </div>
    </section>
  </main>
</template>


