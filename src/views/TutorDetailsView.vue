
<script setup>
import { onMounted, ref } from 'vue';
import { useRoute, RouterLink } from 'vue-router';

const API_URL = 'http://localhost:3000';
const route = useRoute();

const tutor = ref(null);
const loading = ref(true);
const erro = ref('');

async function carregarTutor() {
  try {
    const resposta = await fetch(
      `${API_URL}/tutores/${route.params.id}`
    );

    if (!resposta.ok) {
      throw new Error('Tutor não encontrado');
    }

    tutor.value = await resposta.json();
  } catch (error) {
    console.error(error);
    erro.value = 'Não foi possível encontrar esse tutor.';
  } finally {
    loading.value = false;
  }
}

onMounted(carregarTutor);
</script>

<template>
  <section>
    <h1 class="h2 mb-4">Detalhes do Tutor</h1>

    <p v-if="loading">Carregando tutor...</p>

    <div v-else-if="erro" class="alert alert-danger">
      {{ erro }}
    </div>

    <div v-else-if="tutor" class="card">
      <div class="card-body">
        <h2 class="h4">{{ tutor.nome }}</h2>

        <p><strong>ID:</strong> {{ tutor.id }}</p>
        <p><strong>Nome:</strong> {{ tutor.nome }}</p>
        <p><strong>Telefone:</strong> {{ tutor.telefone }}</p>

        <RouterLink to="/tutores" class="btn btn-secondary">
          Voltar à listagem
        </RouterLink>
      </div>
    </div>
  </section>
</template>
