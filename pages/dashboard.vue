<template>
    <div class="dashboard-container">
      <h1>Dashboard</h1>
      <button @click="logout" class="logout-button">Sair</button>
      <div class="section">
        <h2 class="incomes">Receitas</h2>
        <button @click="createIncome" class="create-button-income">Nova Receita</button>
        <table>
          <thead>
            <tr>
              <th>Categoria</th>
              <th>Valor</th>
              <th>Valor Recebido</th>
              <th>Data de Recebimento</th>
              <th>Ações</th>
            </tr>
          </thead>
          <tbody v-if="incomes.length > 0">
            <tr v-for="income in incomes" :key="income.id">
              <td>{{ income.category }}</td>
              <td>{{ formatCurrency(income.value) }}</td>
              <td>{{ formatCurrency(income.amountReceived) }}</td>
              <td>{{ formatDate(income.receiptDate) }}</td>
              <td>
                <button @click="editIncome(income.id)" class="edit-button">Editar</button>
                <button @click="confirmDeleteIncome(income.id)" class="delete-button">Excluir</button>
              </td>
            </tr>
          </tbody>
          <tbody v-else-if="incomes.length == 0">
            <tr>
              <td colspan="5" style="text-align: center;">Nenhuma receita registrada</td>
            </tr>
          </tbody>
        </table>
      </div>
      <div class="section">
        <h2 class="expenses">Despesas</h2>
        <button @click="createExpense" class="create-button-expense">Nova Despesa</button>
        <table>
          <thead>
            <tr>
              <th>Categoria</th>
              <th>Valor</th>
              <th>Valor Pago</th>
              <th>Data de Vencimento</th>
              <th>Ações</th>
            </tr>
          </thead>
          <tbody v-if="expenses.length > 0">
            <tr v-for="expense in expenses" :key="expense.id">
              <td>{{ expense.category }}</td>
              <td>{{ formatCurrency(expense.value) }}</td>
              <td>{{ formatCurrency(expense.amountPaid) }}</td>
              <td>{{ formatDate(expense.expirationDate) }}</td>
              <td>
                <button @click="editExpense(expense.id)" class="edit-button">Editar</button>
                <button @click="confirmDeleteExpense(expense.id)" class="delete-button">Excluir</button>
              </td>
            </tr>
          </tbody>
          <tbody v-else-if="expenses.length == 0">
            <tr>
              <td colspan="5" style="text-align: center;">Nenhuma despesa registrada</td>
            </tr>
          </tbody>
        </table>
      </div>
      <div v-if="showDeleteModal" class="modal-overlay">
        <div class="modal">
          <h2>Confirmação</h2>
          <p>Tem certeza de que deseja excluir este item?</p>
          <button @click="deleteItem" class="confirm-button">Confirmar</button>
          <button @click="closeDeleteModal" class="cancel-button">Cancelar</button>
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue'
  import axios from 'axios'
  import { useRouter } from 'vue-router'
  import { definePageMeta } from '#imports'
  
  const incomes = ref([])
  const expenses = ref([])
  
  const router = useRouter()
  
  const showDeleteModal = ref(false)
  const itemToDelete = ref(null)
  const deleteType = ref('')
  
  const formatCurrency = (value) => {
    return new Intl.NumberFormat('pt-BR', { style: 'currency', currency: 'BRL' }).format(value)
  }
  
  const formatDate = (dateString) => {
    const date = new Date(dateString)
    const utcDate = new Date(date.getTime() + date.getTimezoneOffset() * 60000)
    const day = String(utcDate.getUTCDate()).padStart(2, '0')
    const month = String(utcDate.getUTCMonth() + 1).padStart(2, '0')
    const year = utcDate.getUTCFullYear()
    return `${day}/${month}/${year}`
  }
  
  const fetchIncomes = async () => {
    const token = localStorage.getItem('token')
    if (!token) {
      throw new Error('Token não encontrado')
    }
    try {
      const response = await axios.get('http://localhost:3333/api/incomes/', {
        headers: {
          Authorization: `Bearer ${token}`
        }
      })
      incomes.value = response.data
    } catch (error) {
      alert('Erro ao carregar receitas')
    }
  }
  
  const fetchExpenses = async () => {
    const token = localStorage.getItem('token')
    if (!token) {
      throw new Error('Token não encontrado')
    }
    try {
      const response = await axios.get('http://localhost:3333/api/expenses/', {
        headers: {
          Authorization: `Bearer ${token}`
        }
      })
      expenses.value = response.data
    } catch (error) {
      alert('Erro ao carregar despesas')
    }
  }
  
  const editIncome = (id) => {
    router.push(`/incomes/edit/${id}`)
  }
  
  const editExpense = (id) => {
    router.push(`/expenses/edit/${id}`)
  }
  
  const createIncome = () => {
    router.push('/incomes/create')
  }
  
  const createExpense = () => {
    router.push('/expenses/create')
  }
  
  const confirmDeleteIncome = (id) => {
    itemToDelete.value = id
    deleteType.value = 'income'
    showDeleteModal.value = true
  }
  
  const confirmDeleteExpense = (id) => {
    itemToDelete.value = id
    deleteType.value = 'expense'
    showDeleteModal.value = true
  }
  
  const deleteItem = async () => {
    const token = localStorage.getItem('token')
    if (!token) {
      throw new Error('Token não encontrado')
    }
    try {
      if (deleteType.value === 'income') {
        await axios.delete(`http://localhost:3333/api/incomes/${itemToDelete.value}`, {
          headers: {
            Authorization: `Bearer ${token}`
          }
        })
        fetchIncomes()
      } else if (deleteType.value === 'expense') {
        await axios.delete(`http://localhost:3333/api/expenses/${itemToDelete.value}`, {
          headers: {
            Authorization: `Bearer ${token}`
          }
        })
        fetchExpenses()
      }
      closeDeleteModal()
    } catch (error) {
      alert('Erro ao excluir item')
    }
  }
  
  const closeDeleteModal = () => {
    showDeleteModal.value = false
    itemToDelete.value = null
    deleteType.value = ''
  }
  
  const logout = () => {
    localStorage.removeItem('token')
    router.push('/login')
  }
  
  onMounted(() => {
    fetchIncomes()
    fetchExpenses()
  })
  
  definePageMeta({
    middleware: 'auth'
  })
  </script>
  
  <style scoped>
  .dashboard-container {
    max-width: 900px;
    width: 100%;
    height: 100vh;
    display: flex;
    flex-direction: column;
    justify-content: center;
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
  
  .logout-button {
    background-color: #dc3545;
    color: #fff;
    padding: 10px 20px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    margin-bottom: 20px;
    align-self: flex-end;
  }
  
  .logout-button:hover {
    background-color: #c82333;
  }
  
  .incomes {
    color: green;
  }
  
  .expenses {
    color: red;
  }
  
  .section {
    margin-bottom: 30px;
  }
  
  table {
    width: 100%;
    border-collapse: collapse;
    margin-bottom: 20px;
  }
  
  th,
  td {
    padding: 10px;
    border: 1px solid #ccc;
    text-align: left;
  }
  
  th {
    background-color: #f2f2f2;
  }
  
  .edit-button,
  .delete-button,
  .create-button-income,
  .create-button-expense {
    padding: 5px 10px;
    margin-right: 5px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }
  
  .edit-button {
    background-color: #007bff;
    color: #fff;
  }
  
  .edit-button:hover {
    background-color: #0056b3;
  }
  
  .delete-button {
    background-color: #dc3545;
    color: #fff;
  }
  
  .delete-button:hover {
    background-color: #c82333;
  }
  
  .create-button-income {
    background-color: #1e7733;
    color: #fff;
    margin-bottom: 10px;
  }
  
  .create-button-expense {
    background-color: #a7282e;
    color: #fff;
    margin-bottom: 10px;
  }
  
  .create-button-income:hover {
    background-color: #175926;
  }
  
  .create-button-expense:hover {
    background-color: #841e23;
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
  
  .confirm-button,
  .cancel-button {
    padding: 10px 20px;
    margin: 10px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }
  
  .confirm-button {
    background-color: #007bff;
    color: #fff;
  }
  
  .confirm-button:hover {
    transition: 0.4s;
    background-color: #0056b3;
  }
  
  .cancel-button {
    color: #fff;
    background-color: #dc3545;
  }
  
  .cancel-button:hover {
    transition: 0.4s;
    background-color: #9b3640;
  }
  </style>