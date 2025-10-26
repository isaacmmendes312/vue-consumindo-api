<script setup>
// 1. IMPORTAÇÕES
// 'ref' é como criamos uma variável "reativa" que o Vue pode observar.
// 'onMounted' é uma função que roda automaticamente quando o componente é "montado" (carregado) na tela.
import { ref, onMounted } from 'vue'

// 2. ESTADO
// Criamos uma lista "posts" reativa, que começa como um array vazio.
// É aqui que vamos armazenar os dados que vêm da API.
const posts = ref([])
const loading = ref(true)

// 3. FUNÇÃO FETCH (BUSCAR DADOS)
// Criamos uma função assíncrona para buscar os dados.
async function fetchPosts() {
  loading.value = true // Começamos o carregamento
  try {
    // Usamos o 'fetch' (nativo do navegador) para "bater" na URL da API.
    const response = await fetch('https://jsonplaceholder.typicode.com/posts')
    // Convertemos a resposta em JSON.
    const data = await response.json()
    // Atualizamos nossa variável "posts" com os dados recebidos.
    posts.value = data
  } catch (error) {
    console.error('Erro ao buscar posts:', error)
  } finally {
    loading.value = false // Terminamos o carregamento (com sucesso ou erro)
  }
}

// 4. CICLO DE VIDA
// Dizemos ao Vue para executar a função 'fetchPosts' assim que o componente for carregado.
onMounted(() => {
  fetchPosts()
})
</script>

<template>
  <div id="app">
    <header>
      <h1>Meu Blog (Consumindo API)</h1>
    </header>
    
    <main>
      <div v-if="loading" class="loading">
        Carregando posts...
      </div>

      <ul v-else class="post-list">
        <li v-for="post in posts" :key="post.id" class="post-item">
          <h2>{{ post.title }}</h2>
          <p>{{ post.body }}</p>
        </li>
      </ul>
    </main>
  </div>
</template>

<style>
/* 8. ESTILIZAÇÃO (CSS) */
/* Isso faz o projeto parecer um pouco melhor */
body {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  background-color: #f4f4f4;
  margin: 0;
  padding: 0;
}

#app {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

header {
  background-color: #42b983; /* Cor do Vue */
  color: white;
  padding: 20px;
  text-align: center;
  border-radius: 8px;
  margin-bottom: 20px;
}

.loading {
  text-align: center;
  font-size: 1.5em;
  padding: 50px;
}

.post-list {
  list-style: none;
  padding: 0;
}

.post-item {
  background-color: white;
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 20px;
  margin-bottom: 15px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.post-item h2 {
  margin-top: 0;
  text-transform: capitalize; /* Coloca a primeira letra de cada palavra em maiúsculo */
}
</style>