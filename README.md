# Front-End de Lista de Tarefas (Vue.js)

Este é um projeto de portfólio que demonstra um front-end completo construído com Vue.js 3. O objetivo principal deste app é consumir uma API RESTful de back-end para realizar operações de **CRUD** (Create, Read, Update, Delete) em uma lista de tarefas.

Este projeto foi construído para funcionar em conjunto com a API .NET que pode ser encontrada aqui:
**[https://github.com/isaacmmendes312/ExemploApiNetBasica](https://github.com/isaacmmendes312/ExemploApiNetBasica)**

---

## Funcionalidades (Features)

* **Create (Criar):** Adicionar novas tarefas à lista através de um formulário.
* **Read (Ler):** Carregar e exibir todas as tarefas existentes do banco de dados.
* **Update (Atualizar):** Marcar tarefas como "Concluídas" ou "Reabrir" (alternando o status).
* **Delete (Deletar):** Remover tarefas da lista (com uma janela de confirmação).

## Tecnologias Utilizadas

* **Vue.js 3** (Composition API com `<script setup>`)
* **JavaScript (ES6+)**
* **Fetch API** (Nativa do navegador) para fazer requisições HTTP (`GET`, `POST`, `PUT`, `DELETE`).
* **CSS3** para estilização responsiva básica.

## Como Rodar Localmente

Para rodar este projeto, você precisará de duas coisas: desta aplicação (front-end) e da API de back-end.

### 1. Pré-requisitos

* [Node.js](https://nodejs.org/) (v16 ou superior)
* A **[API de Back-end](https://github.com/isaacmmendes312/ExemploApiNetBasica)** deve estar rodando em `http://localhost:5123` (ou a porta configurada).

### 2. Rodando o Front-End

```bash
# 1. Clone o repositório
git clone [https://github.com/isaacmmendes312/vue-consumindo-api.git](https://github.com/isaacmmendes312/vue-consumindo-api.git)

# 2. Entre na pasta do projeto
cd vue-consumindo-api

# 3. Instale as dependências
npm install

# 4. Inicie o servidor de desenvolvimento
npm run dev

---

### O Que Fazer Agora (Subir o `README.md`):

1.  **Salve** o arquivo `README.md` no VS Code.
2.  Abra seu terminal na pasta `vue-consumindo-api`.
3.  Envie essa última atualização para o GitHub:

    ```bash
    # 1. Adiciona o README.md modificado
    git add README.md
    
    # 2. Faz o commit
    git commit -m "Atualiza o README.md com descrição completa do projeto"
    
    # 3. Envia para o GitHub
    git push origin main
    ```

Agora, atualize a página do seu GitHub. Você verá uma página inicial bonita e profissional.

Gostaria de fazer o `README.md` para o projeto da API agora?