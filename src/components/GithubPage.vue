<template>
    <div class="container">
      <h1>Busca de Repositórios no GitHub</h1>
  
      <form @submit.prevent="searchRepos">
        <input
          v-model="username"
          type="text"
          placeholder="Digite o nome de usuário do GitHub"
          required
        />
        <button type="submit">Buscar Repositórios</button>
      </form>
  
      <div v-if="loading" class="loading">Carregando...</div>
      <div v-if="error" class="error-message">{{ error }}</div>
  
      <ul v-if="repos.length > 0" class="repo-list">
        <li v-for="repo in repos" :key="repo.id">
          <a :href="repo.html_url" target="_blank">{{ repo.name }}</a>
        </li>
      </ul>
    </div>
  </template>
  
  <script setup>
  import { ref } from "vue";
  
  // Definição das variáveis reativas
  const username = ref("");
  const repos = ref([]);
  const loading = ref(false);
  const error = ref(null);
  
  // Função para buscar repositórios do GitHub
  const searchRepos = async () => {
    if (!username.value) return;
  
    repos.value = [];
    error.value = null;
    loading.value = true;
  
    try {
      const response = await fetch(
        `https://api.github.com/users/${username.value}/repos`
      );
      if (!response.ok) {
        throw new Error("Usuário não encontrado ou erro na requisição");
      }
      const data = await response.json();
      if (data.length === 0) {
        error.value = "Nenhum repositório encontrado para este usuário.";
      } else {
        repos.value = data;
      }
    } catch (err) {
      error.value = err.message;
    } finally {
      loading.value = false;
    }
  };
  </script>
  
  <style scoped>
  /* Estilos básicos */
  body {
    font-family: Arial, sans-serif;
    background-color: #f4f4f9;
    margin: 0;
    padding: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }
  
  .container {
    background-color: #fff;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
    width: 100%;
    max-width: 600px;
  }
  
  h1 {
    text-align: center;
    color: #333;
  }
  
  form {
    display: flex;
    justify-content: space-between;
    margin-bottom: 20px;
  }
  
  input {
    width: 80%;
    padding: 10px;
    font-size: 16px;
    border: 1px solid #ccc;
    border-radius: 4px;
  }
  
  button {
    width: 18%;
    padding: 10px;
    font-size: 16px;
    border: none;
    background-color: #4caf50;
    color: white;
    cursor: pointer;
    border-radius: 4px;
  }
  
  button:hover {
    background-color: #45a049;
  }
  
  .loading {
    display: block;
    text-align: center;
    font-size: 18px;
    color: #007bff;
  }
  
  .error-message {
    color: red;
    text-align: center;
  }
  
  .repo-list {
    list-style: none;
    padding: 0;
  }
  
  .repo-list li {
    margin-bottom: 10px;
  }
  
  .repo-list a {
    text-decoration: none;
    color: #007bff;
  }
  
  .repo-list a:hover {
    text-decoration: underline;
  }
  
  /* Responsividade */
  @media (max-width: 600px) {
    form {
      flex-direction: column;
    }
  
    input,
    button {
      width: 100%;
      margin-bottom: 10px;
    }
  }
  </style>
     