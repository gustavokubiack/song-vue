<template>
    <div class="container d-flex justify-content-center align-items-center min-vh-100">
      <div class="card p-4 shadow-lg" style="width: 100%; max-width: 400px;">
        <h1 class="text-center mb-4">Criar Conta</h1>
        <form @submit.prevent="handleRegister">
          <div class="mb-3">
            <label for="username" class="form-label">Usuário</label>
            <input
              v-model="username"
              type="text"
              class="form-control"
              id="username"
              placeholder="Escolha um nome de usuário"
              required
            />
          </div>
          <div class="mb-3">
            <label for="email" class="form-label">E-mail</label>
            <input
              v-model="email"
              type="email"
              class="form-control"
              id="email"
              placeholder="Digite seu e-mail"
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
              placeholder="Escolha uma senha"
              required
            />
          </div>
          <div class="mb-3">
            <label for="confirmPassword" class="form-label">Confirmar Senha</label>
            <input
              v-model="confirmPassword"
              type="password"
              class="form-control"
              id="confirmPassword"
              placeholder="Confirme sua senha"
              required
            />
          </div>
          <button type="submit" class="btn btn-primary w-100">Registrar</button>
        </form>
        <p v-if="errorMessage" class="text-danger text-center mt-3">{{ errorMessage }}</p>
      </div>
    </div>
  </template>
  
  <script>
  import { createUser } from '@/api/auth';
  
  export default {
    data() {
      return {
        username: '',
        email: '',
        password: '',
        confirmPassword: '',
        errorMessage: '',
      };
    },
    methods: {
      async handleRegister() {
        if (this.password !== this.confirmPassword) {
          this.errorMessage = 'As senhas não coincidem.';
          return;
        }
  
        try {
          const response = await createUser({
            username: this.username,
            email: this.email,
            password: this.password,
          });
          console.log('Usuário criado com sucesso:', response);
          this.errorMessage = '';
  
          this.$router.push({ name: 'Login' });
        } catch (error) {
          console.log(error);
          this.errorMessage = 'Erro ao criar conta. Verifique os dados e tente novamente.';
        }
      },
    },
  };
  </script>
  
  <style scoped>
  .card {
    border-radius: 12px;
  }
  </style>
  