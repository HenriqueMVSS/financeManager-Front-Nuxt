<template>
    <div class="page-container">
      <div class="login-container">
        <h1>Login</h1>
        <form @submit.prevent="login">
          <div class="form-group">
            <label for="email">Email</label>
            <input type="email" v-model="credentials.email" id="email" required />
          </div>
          <div class="form-group">
            <label for="password">Senha</label>
            <input type="password" v-model="credentials.password" id="password" required />
          </div>
          <button type="submit" class="login-button">Login</button>
        </form>
      </div>
  
      <div v-if="showSuccessModal" class="modal-overlay">
        <div class="modal">
          <h2>Seja bem vindo!</h2>
          <p>Login realizado com sucesso!</p>
          <button @click="closeSuccessModal">OK</button>
        </div>
      </div>
  
      <div v-if="showErrorModal" class="modal-overlay">
        <div class="modal">
          <p>Credenciais invalidas</p>
          <button @click="closeErrorModal">OK</button>
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue'
  import { useRouter } from 'vue-router'
  import axios from 'axios'
  
  const credentials = ref({
    email: '',
    password: ''
  })
  
  const router = useRouter()
  const showSuccessModal = ref(false)
  const showErrorModal = ref(false)
  
  const login = async () => {
    try {
      const response = await axios.post('http://localhost:3333/api/login', credentials.value)
      localStorage.setItem('token', response.data.token.token) 
      showSuccessModal.value = true
    } catch (error) {
      showErrorModal.value = true
    }
  }
  
  const closeSuccessModal = () => {
    showSuccessModal.value = false
    router.push('/dashboard')
  }
  
  const closeErrorModal = () => {
    showErrorModal.value = false
  }
  </script>
  
  <style scoped>
  .page-container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    background-color: #f5f5f5;
  }
  
  .login-container {
    max-width: 400px;
    width: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 20px;
    border: 1px solid #ccc;
    border-radius: 8px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    background-color: #fff;
  }
  
  h1 {
    text-align: center;
    margin-bottom: 20px;
  }
  
  .form-group {
    margin-bottom: 15px;
  }
  
  label {
    display: block;
    margin-bottom: 5px;
    font-weight: bold;
  }
  
  input {
    width: 100%;
    padding: 8px;
    box-sizing: border-box;
    border: 1px solid #ccc;
    border-radius: 4px;
  }
  
  .login-button {
    width: 100%;
    padding: 10px;
    background-color: #007bff;
    color: #fff;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 16px;
  }
  
  .login-button:hover {
    background-color: #0056b3;
  }
  
  .modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
  }
  
  .modal {
    background-color: #fff;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    text-align: center;
  }
  
  .modal button {
    margin-top: 20px;
    padding: 10px 20px;
    background-color: #007bff;
    color: #fff;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }
  
  .modal button:hover {
    background-color: #0056b3;
  }
  </style>