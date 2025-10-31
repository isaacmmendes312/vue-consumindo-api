<script setup>
import { ref, onMounted } from 'vue'

// 1. DEFINIR A URL DA API
const API_URL = "http://localhost:5149/api/tarefas" 

// 2. ESTADO
const tarefas = ref([])
const loading = ref(true)

const newTarefaTitulo = ref('')
const isSubmitting = ref(false)


// 3. FUNÇÃO FETCH
async function fetchAllTarefas() {
  loading.value = true
  try {
    const response = await fetch(API_URL)
    const data = await response.json()
    tarefas.value = data
  } catch (error) {
    console.error('Erro ao buscar tarefas:', error)
  } finally {
    loading.value = false
  }
}

// 4. FUNÇÃO CREATE
async function createTarefa() {
  if (isSubmitting.value) return 

  isSubmitting.value = true

  try {
    const response = await fetch(API_URL, {
      method: 'POST',
      body: JSON.stringify({
        titulo: newTarefaTitulo.value,
        concluida: false
      }),
      headers: {
        'Content-Type': 'application/json; charset=UTF-8',
      },
    })

    if (!response.ok) {
      throw new Error('Falha ao criar a tarefa')
    }

    const createdTarefa = await response.json()
    tarefas.value.unshift(createdTarefa) // Adiciona na lista local
    newTarefaTitulo.value = '' // Limpa o campo

  } catch (error) {
    console.error('Erro ao criar tarefa:', error)
  } finally {
    isSubmitting.value = false
  }
}

// 6. FUNÇÃO PARA ATUALIZAR (TOGGLE) O STATUS
async function toggleConcluida(tarefa) {
  try {
    const response = await fetch(`${API_URL}/${tarefa.id}`, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json',
      }
    });

    if (!response.ok) {
      throw new Error('Falha ao atualizar a tarefa');
    }

    const tarefaAtualizada = await response.json();

    const index = tarefas.value.findIndex(t => t.id === tarefaAtualizada.id);
    if (index !== -1) {
      tarefas.value[index] = tarefaAtualizada;
    }

  } catch (error) {
    console.error('Erro ao atualizar tarefa:', error);
  }
}

// 7. DELETAR TAREFA
async function deleteTarefa(tarefaId) {
  if (!confirm("Tem certeza que deseja deletar esta tarefa?")) {
    return;
  }
  try {
    const response = await fetch(`${API_URL}/${tarefaId}`, {
      method: 'DELETE',
    });
    if (!response.ok) {
      throw new Error('Falha ao deletar a tarefa');
    }
    tarefas.value = 
    tarefas.value.filter(t => t.id !== tarefaId);
  } catch (error) {
    console.error('Erro ao deletar tarefa:', error);
  }
}

// 5. CICLO DE VIDA
onMounted(() => {
  fetchAllTarefas()
})
</script>

<template>
  <div id="app">
    <header>
      <h1>Lista de Tarefas</h1>
    </header>

    <section class="form-container">
      <h2>Nova Tarefa</h2>
      <form @submit.prevent="createTarefa">
        <div class="form-group">
          <label for="tarefa-titulo">Título:</label>
          <input 
            type="text" 
            id="tarefa-titulo" 
            v-model="newTarefaTitulo" 
            required 
          />
        </div>
        <button type="submit" :disabled="isSubmitting">
          {{ isSubmitting ? 'Salvando...' : 'Adicionar Tarefa' }}
        </button>
      </form>
    </section>

    <main>
      <h2>Minhas Tarefas</h2>
      
      <div v-if="loading" class="loading">
        Carregando...
      </div>
      
      <div v-if="!loading && tarefas.length === 0" class="empty-list">
        Nenhuma tarefa encontrada. Adicione uma!
      </div>
      
      <ul class="post-list">
        <li v-for="tarefa in tarefas" :key="tarefa.id" class="post-item">
          
          <span :class="{ 'tarefa-concluida': tarefa.concluida }">
            {{ tarefa.titulo }}
          </span>

          <div class="botoes-acao">
            <button @click="toggleConcluida(tarefa)"
                    :class="['toggle-btn', tarefa.concluida ? 'btn-reabrir' : 'btn-concluir']">
              
              {{ tarefa.concluida ? 'Reabrir' : '✔ Concluir' }}

            </button>

            <button @click="deleteTarefa(tarefa.id)" class="toggle-btn btn-deletar">
              Deletar
            </button>
          </div>

        </li>
      </ul>
    </main>
  </div>
</template>

<style>
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
  background-color: #34495e;
  color: white;
  padding: 20px;
  text-align: center;
  border-radius: 8px;
  margin-bottom: 20px;
}
h2 {
  border-bottom: 2px solid #34495e;
  padding-bottom: 5px;
}
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
.form-group input[type="text"] {
  width: 95%;
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
}
button:disabled {
  background-color: #aaa;
}

.loading, .empty-list {
  text-align: center;
  font-size: 1.2em;
  padding: 40px;
  color: #777;
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
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.tarefa-concluida {
  text-decoration: line-through;
  color: #999;
}
.status {
  font-size: 0.9em;
  font-weight: bold;
  color: #777;
}
.toggle-btn {
  padding: 5px 10px;
  font-size: 0.9em;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  color: white;
  transition: background-color 0.2s;
}
.btn-concluir {
  background-color: #42b983;
}
.btn-concluir:hover {
  background-color: #369b70;
}
.btn-reabrir {
  background-color: #f39c12;
}
.btn-reabrir:hover {
  background-color: #d35400;
}
.botoes-acao {
  display: flex;
  gap: 10px;
}
.btn-deletar {
  background-color: #e74c3c;
}
.btn-deletar:hover {
  background-color: #c0392b;
}
</style>