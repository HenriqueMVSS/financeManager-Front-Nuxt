<!-- filepath: /Users/henriquemateus/Projetos/finance-frontend/pages/expenses/create.vue -->
<template>
  <div class="create-container">
    <h1>Registrar Despesa</h1>
    <form @submit.prevent="submitExpense">
      <div class="form-group">
        <label for="category">Categoria</label>
        <input type="text" v-model="expense.category" id="category" required />
      </div>
      <div class="form-group">
        <label for="value">Valor</label>
        <input type="number" v-model="expense.value" id="value" required />
      </div>
      <div class="form-group">
        <label for="amountSpent">Valor Gasto</label>
        <input type="number" v-model="expense.amountPaid" id="amountSpent" required />
      </div>
      <div class="form-group">
        <label for="expenseDate">Data da Despesa</label>
        <input type="date" v-model="expense.expirationDate" id="expenseDate" required />
      </div>
      <div class="form-actions">
        <button type="submit" class="submit-button">Registrar</button>
        <button @click="goBack" type="button" class="back-button">Voltar</button>
      </div>
    </form>

    <div v-if="showSuccessModal" class="modal-overlay">
      <div class="modal">
        <h2>Sucesso</h2>
        <p>Despesa registrada com sucesso!</p>
        <button @click="closeSuccessModal" class="confirm-button">OK</button>
      </div>
    </div>

    <div v-if="showErrorModal" class="modal-overlay">
      <div class="modal">
        <h2>Erro</h2>
        <p>{{ errorMessage }}</p>
        <button @click="closeErrorModal" class="confirm-button">OK</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'
import { definePageMeta } from '#imports'
import { useRouter } from 'vue-router'

const router = useRouter()

definePageMeta({
  middleware: 'auth'
})

const expense = ref({
  category: '',
  value: 0,
  amountPaid: 0,
  expirationDate: ''
})

const showSuccessModal = ref(false)
const showErrorModal = ref(false)
const errorMessage = ref('')

const submitExpense = async () => {
  const token = localStorage.getItem('token')
  if (!token) {
    throw new Error('Token não encontrado')
  }
  try {
    await axios.post('http://localhost:3333/api/expenses', expense.value, {
      headers: {
        Authorization: `Bearer ${token}`
      }
    })
    showSuccessModal.value = true
  } catch (error) {
    errorMessage.value = 'Erro ao registrar despesa'
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

const goBack = () => {
  router.push('/dashboard')
}
</script>

<style scoped>
.create-container {
  max-width: 600px;
  width: 100%;
  margin: 0 auto;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  font-family: sans-serif;
}

h1 {
  text-align: center;
  margin-bottom: 20px;
  color: #0056b3;
}

.form-group {
  margin-bottom: 15px;
}

label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}

input[type="text"],
input[type="number"],
input[type="date"] {
  width: 100%;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
}

.form-actions {
  display: flex;
  justify-content: space-between;
}

.submit-button,
.back-button {
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.submit-button {
  background-color: #007bff;
  color: #fff;
}

.submit-button:hover {
  background-color: #0056b3;
}

.back-button {
  background-color: #6c757d;
  color: #fff;
}

.back-button:hover {
  background-color: #5a6268;
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
  max-width: 400px;
  width: 100%;
}

.confirm-button {
  padding: 10px 20px;
  margin-top: 10px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  background-color: #007bff;
  color: #fff;
}

.confirm-button:hover {
  background-color: #0056b3;
}
</style>