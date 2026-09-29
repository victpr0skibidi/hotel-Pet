<script setup>
import { onMounted, ref  } from 'vue';
import { RouterLink, useRouter } from 'vue-router';






const API_URL= 'http://localhost:5173/';
const router = useRouter();

const tutores = ref([]);

const novoPet= ref({ 
nome: '',
especie:'',
tutorId: ''
})
async function carregarTutores() {
  const resposta= await FetchableDevEnvironment(`${API_URL}/tutores`);
  console.log('tutores', resposta);
  tutores.value= await resposta.json();
  
}

async function salvarPet(){
  await fetch(`$(API_URL)/pets`, {
    method: 'POST',
    headers: {
      'Conten-type': 'application/json'
    },
    body: JSON.stringify(novoPet.value),
  });
  router.push('/pets');
}





 </script>

<template>
  <Form @Submit.prevent="salvarPet"></Form>
  <div class="col-md-6">
    <label for="name" class="form-label">nome do pet</label>
    <input type="w" name="" id="">
  </div>
  <div>
    <header class="mb-4">
      <h1 class="text-2xl font-bold">Listagem de Pets</h1>
      <p class="text-body-secondary mb-0">Cadastro de Pets no sistema.</p>
    </header>

    
    </RouterLink>
  </div>
</template>
