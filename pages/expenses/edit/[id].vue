<template>
  <div class="edit-container">
    <h1>Editar Despesa</h1>
    <form @submit.prevent="updateExpense">
      <div class="form-group">
        <label for="category">Categoria</label>
        <input type="text" v-model="expense.category" id="category" required />
      </div>
      <div class="form-group">
        <label for="value">Valor</label>
        <input type="number" v-model="expense.value" id="value" required />
      </div>
      <div class="form-group">
        <label for="amountPaid">Valor Gasto</label>
        <input type="number" v-model="expense.amountPaid" id="amountPaid" required />
      </div>
      <div class="form-group">
        <label for="expirationDate">Data da Despesa</label>
        <input type="date" v-model="expense.expirationDate" id="expirationDate" required />
      </div>
      <div class="form-actions">
        <button type="submit" class="update-button">Atualizar</button>
        <button @click="goBack" type="button" class="back-button">Voltar</button>
      </div>
    </form>

    <div v-if="showSuccessModal" class="modal-overlay">
      <div class="modal">
        <h2>Sucesso</h2>
        <p>Despesa atualizada com sucesso!</p>
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
import { ref, onMounted } from 'vue'
import axios from 'axios'
import { useRoute, useRouter } from 'vue-router'
import { definePageMeta } from '#imports'

definePageMeta({
  middleware: 'auth'
})

const expense = ref({
  category: '',
  value: 0,
  amountPaid: 0,
  expirationDate: ''
})

const route = useRoute()
const router = useRouter()

const showSuccessModal = ref(false)
const showErrorModal = ref(false)
const errorMessage = ref('')

const fetchExpense = async () => {
  try {
    const token = localStorage.getItem('token')
    if (!token) {
      throw new Error('Token não encontrado')
    }
    const response = await axios.get(`http://localhost:3333/api/expenses/${route.params.id}`, {
      headers: {
        Authorization: `Bearer ${token}`
      }
    })
    const data = response.data
    data.expirationDate = data.expirationDate.split('T')[0]
    expense.value = data
  } catch (error) {
    errorMessage.value = 'Erro ao carregar despesa'
    showErrorModal.value = true
  }
}

const updateExpense = async () => {
  try {
    const token = localStorage.getItem('token')
    if (!token) {
      throw new Error('Token não encontrado')
    }
    await axios.put(`http://localhost:3333/api/expenses/${route.params.id}`, expense.value, {
      headers: {
        Authorization: `Bearer ${token}`
      }
    })
    showSuccessModal.value = true
  } catch (error) {
    errorMessage.value = 'Erro ao atualizar despesa'
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

onMounted(fetchExpense)
</script>

<style scoped>
.edit-container {
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

.update-button,
.back-button {
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.update-button {
  background-color: #007bff;
  color: #fff;
}

.update-button:hover {
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