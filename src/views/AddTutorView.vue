
<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';

const API_URL = 'http://localhost:3000';
const router = useRouter();

const novoTutor = ref({
  nome: '',
  telefone: '',
});

async function salvarTutor() {
  try {
    const resposta = await fetch(`${API_URL}/tutores`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(novoTutor.value),
    });

    if (!resposta.ok) {
      throw new Error('Erro ao cadastrar tutor');
    }

    router.push('/tutores');
  } catch (error) {
    console.error(error);
    alert('Não foi possível cadastrar o tutor.');
  }
}
</script>

<template>
  <div>
    <header class="mb-4">
      <h1 class="h2">Cadastrar Tutor</h1>
      <p class="text-body-secondary">
        Preencha os dados do tutor.
      </p>
    </header>

    <form @submit.prevent="salvarTutor">
      <div class="mb-3">
        <label for="nome" class="form-label">Nome</label>
        <input
          id="nome"
          v-model.trim="novoTutor.nome"
          type="text"
          class="form-control"
          required
        />
      </div>

      <div class="mb-3">
        <label for="telefone" class="form-label">Telefone</label>
        <input
          id="telefone"
          v-model.trim="novoTutor.telefone"
          type="tel"
          class="form-control"
          placeholder="(47) 99999-9999"
          required
        />
      </div>

      <button type="submit" class="btn btn-success">
        Salvar tutor
      </button>

      <RouterLink to="/tutores" class="btn btn-secondary ms-2">
        Cancelar
      </RouterLink>
    </form>
  </div>
</template>

<script>
import { RouterLink } from 'vue-router';
</script>
