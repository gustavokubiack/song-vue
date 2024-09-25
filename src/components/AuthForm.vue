<template>
    <div class="container d-flex justify-content-center align-items-center min-vh-100">
      <div class="card p-4 shadow-lg" style="width: 100%; max-width: 400px;">
        <h1 class="text-center mb-4">Login</h1>
        <form @submit.prevent="handleLogin">
          <div class="mb-3">
            <label for="username" class="form-label">Usuário</label>
            <input
              v-model="username"
              type="text"
              class="form-control"
              id="username"
              placeholder="Insira seu usuário"
              required
            />
          </div>
          <div class="mb-3">
            <label for="password" class="form-label">Senha</label>
            <input
              v-model="password"
              type="password"
              class="form-control"
              id="password"
              placeholder="Insira sua senha"
              required
            />
          </div>
          <button type="submit" class="btn btn-primary w-100">Login</button>
          <div class="text-center">
            <router-link to="/register" class="btn btn-link">Não tem uma conta? Cadastrar-se</router-link>
          </div>
        </form>
        <p v-if="errorMessage" class="text-danger text-center mt-3">{{ errorMessage }}</p>
      </div>
    </div>
  </template>
  
  <script>
  import { loginUser } from '@/api/auth';
  
  export default {
    data() {
      return {
        username: '',
        password: '',
        errorMessage: '',
      };
    },
    methods: {
    async handleLogin() {
      try {
        const response = await loginUser(this.username, this.password);
        console.log('Login bem-sucedido:', response);
        this.errorMessage = '';
        
        this.$router.push({ name: 'Home' });
      } catch (error) {
        console.log(error);
        this.errorMessage = 'Login failed. Please check your credentials.';
      }
    },
  },
    }
  </script>
  
  <style scoped>
  .card {
    border-radius: 12px;
  }
  </style>
  