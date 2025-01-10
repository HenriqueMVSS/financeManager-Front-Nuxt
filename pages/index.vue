<template>
    <div>
      <h1>Gestão de Finanças</h1>
    </div>
  </template>
  
  <script setup>
  import { useRouter } from 'vue-router'
  import { onBeforeMount } from 'vue'
  
  const router = useRouter()
  
  onBeforeMount(() => {
    router.push('/login')
  })
  </script>