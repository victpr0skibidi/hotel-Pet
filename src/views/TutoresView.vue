<script setup>
import { computed, onMounted, ref } from 'vue';
import { RouterLink } from 'vue-router';

const API_URL = 'http://localhost:3000';

const tutores = ref([]);
const pets = ref([]);
const loading = ref(true);
const busca = ref('');

async function carregarDados() {
  try {
    const [respostaTutores, respostaPets] = await Promise.all([
      fetch(`${API_URL}/tutores`),
      fetch(`${API_URL}/pets`),
    ]);

    if (!respostaTutores.ok || !respostaPets.ok) {
      throw new Error('Erro ao carregar os dados');
    }

    tutores.value = await respostaTutores.json();
    pets.value = await respostaPets.json();
  } catch (error) {
    console.error(error);
    alert('Não foi possível carregar os tutores.');
  } finally {
    loading.value = false;
  }
}

function petsDoTutor(tutorId) {
  return pets.value.filter((pet) => String(pet.tutorId) === String(tutorId));
}

function mascararDocumento(documento) {
  const digitos = String(documento ?? '').replace(/\D/g, '');
  return digitos.length > 11 ? '**.***.***/****-**' : '***.***.***-**';
}

const tutoresFiltrados = computed(() => {
  const termo = busca.value.trim().toLowerCase();
  if (!termo) return tutores.value;

  const termoNumerico = termo.replace(/\D/g, '');

  return tutores.value.filter((tutor) => {
    const nome = (tutor.nome ?? '').toLowerCase();
    const email = (tutor.email ?? '').toLowerCase();
    const documento = String(tutor.cpfCnpj ?? '').replace(/\D/g, '');

    return (
      nome.includes(termo) ||
      email.includes(termo) ||
      (termoNumerico !== '' && documento.includes(termoNumerico))
    );
  });
});

async function excluirTutor(tutor) {
  if (petsDoTutor(tutor.id).length > 0) {
    alert(
      'Este tutor possui pets vinculados. Remova ou transfira os pets antes de excluí-lo.',
    );
    return;
  }

  const confirmar = window.confirm(
    `Tem certeza de que deseja remover o tutor "${tutor.nome}"?`,
  );
  if (!confirmar) return;

  try {
    const resposta = await fetch(`${API_URL}/tutores/${tutor.id}`, {
      method: 'DELETE',
    });

    if (!resposta.ok) {
      throw new Error('Erro ao remover tutor');
    }

    await carregarDados();
  } catch (error) {
    console.error(error);
    alert('Não foi possível remover o tutor.');
  }
}

onMounted(carregarDados);
</script>

<template>
  <div>
    <header
      class="d-flex flex-wrap justify-content-between align-items-start gap-3 mb-4"
    >
      <div>
        <h1 class="h2 fw-bold mb-1">Listagem de Tutores</h1>
        <p class="text-body-secondary mb-0">
          Consulte os tutores cadastrados e seus pets.
        </p>
      </div>

      <RouterLink
        to="/tutores/novo"
        class="btn btn-primary"
      >
        <i
          class="bi bi-plus-lg me-2"
          aria-hidden="true"
        ></i>
        Cadastrar Tutor
      </RouterLink>
    </header>

    <div class="input-group mb-4">
      <span class="input-group-text bg-white">
        <i
          class="bi bi-search"
          aria-hidden="true"
        ></i>
      </span>
      <input
        v-model="busca"
        type="search"
        class="form-control"
        placeholder="Pesquisar tutor por nome, CPF/CNPJ ou e-mail"
        aria-label="Pesquisar tutor"
      />
    </div>

    <p v-if="loading">Carregando tutores...</p>

    <div
      v-else
      class="table-responsive"
    >
      <table class="table table-striped table-hover align-middle border">
        <thead class="table-light">
          <tr>
            <th>ID</th>
            <th>Nome Completo</th>
            <th>CPF / CNPJ</th>
            <th>Telefone / Celular / WhatsApp</th>
            <th>E-mail</th>
            <th class="text-center">Pets (Quantidade)</th>
            <th>Nomes dos Pets</th>
            <th>Ações</th>
          </tr>
        </thead>

        <tbody>
          <tr
            v-for="tutor in tutoresFiltrados"
            :key="tutor.id"
          >
            <td>{{ tutor.id }}</td>
            <td>{{ tutor.nome }}</td>
            <td>{{ mascararDocumento(tutor.cpfCnpj) }}</td>
            <td>{{ tutor.telefone }}</td>
            <td>{{ tutor.email }}</td>
            <td class="text-center">{{ petsDoTutor(tutor.id).length }}</td>
            <td>
              {{
                petsDoTutor(tutor.id).length
                  ? petsDoTutor(tutor.id)
                      .map((pet) => pet.nome)
                      .join(' / ')
                  : 'Nenhum pet vinculado'
              }}
            </td>
            <td class="text-nowrap">
              <RouterLink
                :to="`/tutores/${tutor.id}`"
                class="btn btn-outline-primary btn-sm me-2"
              >
                <i
                  class="bi bi-eye-fill me-1"
                  aria-hidden="true"
                ></i>
                Visualizar
              </RouterLink>

              <button
                type="button"
                class="btn btn-outline-danger btn-sm"
                @click="excluirTutor(tutor)"
              >
                <i
                  class="bi bi-trash me-1"
                  aria-hidden="true"
                ></i>
                Remover
              </button>
            </td>
          </tr>

          <tr v-if="tutoresFiltrados.length === 0">
            <td
              colspan="8"
              class="text-center text-body-secondary py-4"
            >
              Nenhum tutor encontrado.
            </td>
          </tr>
        </tbody>
      </table>

      <p class="text-body-secondary border-top pt-3">
        Exibindo {{ tutoresFiltrados.length }}
        {{ tutoresFiltrados.length === 1 ? 'tutor' : 'tutores' }}
      </p>
    </div>
  </div>
</template>