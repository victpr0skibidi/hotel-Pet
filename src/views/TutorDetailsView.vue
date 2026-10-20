<script setup>
import { computed, onMounted, ref } from 'vue';
import { RouterLink, useRoute, useRouter } from 'vue-router';

const API_URL = 'http://localhost:3000';
const route = useRoute();
const router = useRouter();

const tutor = ref(null);
const pets = ref([]);
const loading = ref(true);
const erro = ref('');

async function carregarDados() {
  try {
    const respostaTutor = await fetch(`${API_URL}/tutores/${route.params.id}`);

    if (!respostaTutor.ok) {
      throw new Error('Tutor não encontrado');
    }

    tutor.value = await respostaTutor.json();

    const respostaPets = await fetch(`${API_URL}/pets`);
    if (respostaPets.ok) {
      const todosPets = await respostaPets.json();
      pets.value = todosPets.filter(
        (pet) => String(pet.tutorId) === String(tutor.value.id),
      );
    }
  } catch (error) {
    console.error(error);
    erro.value = 'Não foi possível encontrar esse tutor.';
  } finally {
    loading.value = false;
  }
}

const documentoMascarado = computed(() => {
  const digitos = String(tutor.value?.cpfCnpj ?? '').replace(/\D/g, '');
  return digitos.length > 11 ? '**.***.***/****-**' : '***.***.***-**';
});

const dataNascimentoFormatada = computed(() => {
  const data = tutor.value?.dataNascimento;
  if (!data) return '—';
  const [ano, mes, dia] = data.split('-');
  return `${dia}/${mes}/${ano}`;
});

async function removerTutor() {
  if (pets.value.length > 0) {
    alert(
      'Este tutor possui pets vinculados. Remova ou transfira os pets antes de excluí-lo.',
    );
    return;
  }

  const confirmar = window.confirm(
    `Tem certeza de que deseja remover o tutor "${tutor.value.nome}"?`,
  );
  if (!confirmar) return;

  try {
    const resposta = await fetch(`${API_URL}/tutores/${tutor.value.id}`, {
      method: 'DELETE',
    });

    if (!resposta.ok) {
      throw new Error('Erro ao remover tutor');
    }

    router.push('/tutores');
  } catch (error) {
    console.error(error);
    alert('Não foi possível remover o tutor.');
  }
}

onMounted(carregarDados);
</script>

<template>
  <section>
    <header
      class="d-flex flex-wrap justify-content-between align-items-start gap-3 mb-4"
    >
      <div>
        <h1 class="h2 fw-bold mb-1">Detalhes do Tutor</h1>
        <p class="text-body-secondary mb-0">
          Visualize os dados do tutor e seus pets vinculados.
        </p>
      </div>

      <RouterLink
        to="/tutores"
        class="btn btn-outline-primary"
      >
        <i
          class="bi bi-arrow-left me-2"
          aria-hidden="true"
        ></i>
        Voltar para Tutores
      </RouterLink>
    </header>

    <p v-if="loading">Carregando tutor...</p>

    <div
      v-else-if="erro"
      class="alert alert-danger"
    >
      {{ erro }}
    </div>

    <template v-else-if="tutor">
      <div class="card mb-3">
        <div class="card-header fs-5">Dados do Tutor</div>
        <div class="card-body">
          <div class="row g-3">
            <div class="col-md-4">
              <div class="fw-semibold small">ID</div>
              <div>{{ tutor.id }}</div>
            </div>
            <div class="col-md-4">
              <div class="fw-semibold small">Nome Completo</div>
              <div>{{ tutor.nome }}</div>
            </div>
            <div class="col-md-4">
              <div class="fw-semibold small">CPF / CNPJ</div>
              <div>{{ documentoMascarado }}</div>
            </div>
            <div class="col-md-4">
              <div class="fw-semibold small">Telefone / Celular / WhatsApp</div>
              <div>{{ tutor.telefone }}</div>
            </div>
            <div class="col-md-4">
              <div class="fw-semibold small">E-mail</div>
              <div>{{ tutor.email }}</div>
            </div>
            <div class="col-md-4">
              <div class="fw-semibold small">Data de Nascimento</div>
              <div>{{ dataNascimentoFormatada }}</div>
            </div>
          </div>
        </div>
      </div>

      <div class="card mb-3">
        <div class="card-header fs-5">Endereço</div>
        <div class="card-body">
          <div class="row g-3">
            <div class="col-md-4">
              <div class="fw-semibold small">Rua</div>
              <div>{{ tutor.rua || '—' }}</div>
            </div>
            <div class="col-md-4">
              <div class="fw-semibold small">Número</div>
              <div>{{ tutor.numero || '—' }}</div>
            </div>
            <div class="col-md-4">
              <div class="fw-semibold small">Bairro</div>
              <div>{{ tutor.bairro || '—' }}</div>
            </div>
            <div class="col-md-4">
              <div class="fw-semibold small">Cidade</div>
              <div>{{ tutor.cidade || '—' }}</div>
            </div>
            <div class="col-md-4">
              <div class="fw-semibold small">Estado</div>
              <div>{{ tutor.estado || '—' }}</div>
            </div>
            <div class="col-md-4">
              <div class="fw-semibold small">CEP</div>
              <div>{{ tutor.cep || '—' }}</div>
            </div>
          </div>
        </div>
      </div>

      <div class="card mb-3">
        <div class="card-header fs-5">
          Pets vinculados
          <span class="badge text-bg-primary ms-2">
            {{ pets.length }} {{ pets.length === 1 ? 'pet' : 'pets' }}
          </span>
        </div>

        <table class="table table-striped table-sm mb-0">
          <thead>
            <tr>
              <th class="ps-3">ID</th>
              <th>Nome</th>
              <th>Espécie</th>
              <th>Ações</th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="pet in pets"
              :key="pet.id"
            >
              <td class="ps-3">{{ pet.id }}</td>
              <td>{{ pet.nome }}</td>
              <td>{{ pet.especie }}</td>
              <td>
                <RouterLink
                  :to="`/pets/${pet.id}`"
                  class="btn btn-outline-primary btn-sm"
                >
                  Visualizar Pet
                </RouterLink>
              </td>
            </tr>

            <tr v-if="pets.length === 0">
              <td
                colspan="4"
                class="text-center text-body-secondary py-3"
              >
                Nenhum pet vinculado.
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <button
        type="button"
        class="btn btn-outline-danger"
        @click="removerTutor"
      >
        Remover Tutor
      </button>
    </template>
  </section>
</template>