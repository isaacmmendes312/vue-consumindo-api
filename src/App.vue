<script setup>
// 1. IMPORTAÇÕES
import { ref, onMounted } from 'vue'

// 2. ESTADO
const posts = ref([])
const loading = ref(true)

//Variáveis reativas para os campos do formulário
const newPostTitle = ref('')
const newPostBody = ref('')
// Variável para controlar o estado de "enviando" do formulário
const isSubmitting = ref(false)


// 3. FUNÇÃO FETCH (BUSCAR DADOS)
async function fetchAllPosts() {
  loading.value = true
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/posts')
    const data = await response.json()
    // A API retorna 100 posts, vamos pegar só os 10 primeiros para a lista não ficar gigante
    posts.value = data.slice(0, 10) 
  } catch (error) {
    console.error('Erro ao buscar posts:', error)
  } finally {
    loading.value = false
  }
}

// 4. NOVO: FUNÇÃO CREATE (ENVIAR DADOS)
async function createPost() {
  // Se já estiver enviando, não faça nada (evita cliques duplos)
  if (isSubmitting.value) return 

  isSubmitting.value = true // Ativa o estado de "enviando"

  try {
    // Faz a requisição 'POST', enviando os dados do formulário
    const response = await fetch('https://jsonplaceholder.typicode.com/posts', {
      method: 'POST',
      body: JSON.stringify({
        title: newPostTitle.value, // Pega o valor do input de título
        body: newPostBody.value,   // Pega o valor do textarea
        userId: 1, // A API exige um userId, podemos "fingir" que é o usuário 1
      }),
      headers: {
        'Content-Type': 'application/json; charset=UTF-8',
      },
    })

    if (!response.ok) {
      throw new Error('Falha ao criar o post')
    }

    // A API nos devolve o post que foi "criado" (com um novo ID)
    const createdPost = await response.json()

    // Adiciona o post recém-criado no INÍCIO da nossa lista 'posts'
    // 'unshift' adiciona um item no começo de um array
    posts.value.unshift(createdPost)

    // Limpa os campos do formulário
    newPostTitle.value = ''
    newPostBody.value = ''

  } catch (error) {
    console.error('Erro ao criar post:', error)
  } finally {
    isSubmitting.value = false // Desativa o estado de "enviando"
  }
}

// 5. CICLO DE VIDA
onMounted(() => {
  fetchAllPosts()
})
</script>

<template>
  <div id="app">
    <header>
      <h1>Meu Blog (Consumindo API)</h1>
    </header>

    <section class="form-container">
      <h2>Criar Novo Post</h2>
      <form @submit.prevent="createPost">
        <div class="form-group">
          <label for="post-title">Título:</label>
          <input 
            type="text" 
            id="post-title" 
            v-model="newPostTitle" 
            required 
          />
        </div>
        <div class="form-group">
          <label for="post-body">Conteúdo:</label>
          <textarea 
            id="post-body" 
            v-model="newPostBody" 
            rows="4" 
            required
          ></textarea>
        </div>
        <button type="submit" :disabled="isSubmitting">
          {{ isSubmitting ? 'Enviando...' : 'Criar Post' }}
        </button>
      </form>
    </section>

    <main>
      <h2>Posts Recentes</h2>
      
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
/* 8. ESTILIZAÇÃO */
body {
  font-family: Avenir, Helvetica, Arial, sans-serif;
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
  background-color: #42b983;
  color: white;
  padding: 20px;
  text-align: center;
  border-radius: 8px;
  margin-bottom: 20px;
}

h2 {
  border-bottom: 2px solid #42b983;
  padding-bottom: 5px;
}

/* Estilos do Formulário */
.form-container {
  background-color: white;
  padding: 20px;
  border-radius: 8px;
  margin-bottom: 30px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.form-group {
  margin-bottom: 15px;
}

.form-group label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}

.form-group input[type="text"],
.form-group textarea {
  width: 95%; /* 100% com padding pode estourar */
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 1em;
}

button {
  background-color: #42b983;
  color: white;
  padding: 10px 15px;
  border: none;
  border-radius: 4px;
  font-size: 1em;
  cursor: pointer;
  transition: background-color 0.2s;
}

button:hover {
  background-color: #369b70;
}

button:disabled {
  background-color: #aaa;
  cursor: not-allowed;
}


/* Estilos da Lista */
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
  text-transform: capitalize;
}
</style>