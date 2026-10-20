
<script setup>
import { onMounted, ref } from 'vue';
import { RouterLink } from 'vue-router';

const API_URL = 'http://localhost:3000';

const tutores = ref([]);
const loading = ref(true);

async function carregarTutores() {
  try {
    const resposta = await fetch(`${API_URL}/tutores`);

    if (!resposta.ok) {
      throw new Error('Erro ao carregar tutores');
    }

    tutores.value = await resposta.json();
  } catch (error) {
    console.error(error);
    alert('Não foi possível carregar os tutores.');
  } finally {
    loading.value = false;
  }
}

async function excluirTutor(id) {
  const confirmar = window.confirm(
    'Tem certeza de que deseja excluir este tutor?'
  );

  if (!confirmar) return;

  try {
    const resposta = await fetch(`${API_URL}/tutores/${id}`, {
      method: 'DELETE',
    });

    if (!resposta.ok) {
      throw new Error('Erro ao excluir tutor');
    }

    await carregarTutores();
  } catch (error) {
    console.error(error);
    alert('Não foi possível excluir o tutor.');
  }
}

onMounted(carregarTutores);
</script>

<template>
  <div>
    <header class="mb-4">
      <h1 class="h2">Listagem de Tutores</h1>
      <p class="text-body-secondary">
        Tutores cadastrados no sistema.
      </p>
    </header>

    <RouterLink to="/tutores/novo" class="btn btn-success mb-3">
      Adicionar Tutor
    </RouterLink>

    <p v-if="loading">Carregando tutores...</p>

    <div v-else class="table-responsive">
      <table class="table table-striped table-hover">
        <thead>
          <tr>
            <th>ID</th>
            <th>Nome</th>
            <th>Telefone</th>
            <th>Ações</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="tutor in tutores" :key="tutor.id">
            <td>{{ tutor.id }}</td>
            <td>{{ tutor.nome }}</td>
            <td>{{ tutor.telefone }}</td>
            <td>
              <div class="d-flex gap-2">
                <RouterLink
                  :to="`/tutores/${tutor.id}`"
                  class="btn btn-primary btn-sm"
                >
                  Visualizar
                </RouterLink>

                <button
                  class="btn btn-danger btn-sm"
                  @click="excluirTutor(tutor.id)"
                >
                  Excluir
                </button>
              </div>
            </td>
          </tr>

          <tr v-if="tutores.length === 0">
            <td colspan="4" class="text-center">
              Nenhum tutor cadastrado.
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>
