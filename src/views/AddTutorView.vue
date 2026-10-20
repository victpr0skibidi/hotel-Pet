<script setup>
import { ref } from 'vue';
import { RouterLink, useRouter } from 'vue-router';

const API_URL = 'http://localhost:3000';
const router = useRouter();

const estados = [
  'AC', 'AL', 'AP', 'AM', 'BA', 'CE', 'DF', 'ES', 'GO', 'MA', 'MT', 'MS',
  'MG', 'PA', 'PB', 'PR', 'PE', 'PI', 'RJ', 'RN', 'RS', 'RO', 'RR', 'SC',
  'SP', 'SE', 'TO',
];

const novoTutor = ref({
  nome: '',
  cpfCnpj: '',
  telefone: '',
  email: '',
  dataNascimento: '',
  rua: '',
  numero: '',
  bairro: '',
  cidade: '',
  estado: '',
  cep: '',
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
      <h1 class="h2 fw-bold mb-1">Cadastrar Tutor</h1>
      <p class="text-body-secondary mb-0">
        Preencha os dados para cadastrar um novo tutor.
      </p>
    </header>

    <form @submit.prevent="salvarTutor">
      <h2 class="h5 fw-bold border-bottom pb-1 mb-3">Dados do Tutor</h2>

      <div class="row g-3 mb-4">
        <div class="col-md-5">
          <label
            for="id"
            class="form-label fw-semibold small"
            >ID</label
          >
          <input
            id="id"
            type="text"
            class="form-control"
            placeholder="Gerado automaticamente"
            disabled
          />
        </div>

        <div class="col-md-7">
          <label
            for="nome"
            class="form-label fw-semibold small"
            >Nome Completo</label
          >
          <input
            id="nome"
            v-model.trim="novoTutor.nome"
            type="text"
            class="form-control"
            placeholder="Digite o nome completo"
            required
          />
        </div>

        <div class="col-md-6">
          <label
            for="cpfCnpj"
            class="form-label fw-semibold small"
            >CPF / CNPJ</label
          >
          <input
            id="cpfCnpj"
            v-model.trim="novoTutor.cpfCnpj"
            type="text"
            class="form-control"
            placeholder="Digite o CPF ou CNPJ"
            required
          />
        </div>

        <div class="col-md-6">
          <label
            for="telefone"
            class="form-label fw-semibold small"
            >Telefone / Celular / WhatsApp</label
          >
          <input
            id="telefone"
            v-model.trim="novoTutor.telefone"
            type="tel"
            class="form-control"
            placeholder="(00) 00000-0000"
            required
          />
        </div>

        <div class="col-md-6">
          <label
            for="email"
            class="form-label fw-semibold small"
            >E-mail</label
          >
          <input
            id="email"
            v-model.trim="novoTutor.email"
            type="email"
            class="form-control"
            placeholder="nome@example.com"
            required
          />
        </div>

        <div class="col-md-6">
          <label
            for="dataNascimento"
            class="form-label fw-semibold small"
            >Data de Nascimento</label
          >
          <input
            id="dataNascimento"
            v-model="novoTutor.dataNascimento"
            type="date"
            class="form-control"
          />
        </div>
      </div>

      <h2 class="h5 fw-bold border-bottom pb-1 mb-3">Endereço</h2>

      <div class="row g-3 mb-4">
        <div class="col-md-9">
          <label
            for="rua"
            class="form-label fw-semibold small"
            >Rua</label
          >
          <input
            id="rua"
            v-model.trim="novoTutor.rua"
            type="text"
            class="form-control"
            placeholder="Digite o nome da rua"
          />
        </div>

        <div class="col-md-3">
          <label
            for="numero"
            class="form-label fw-semibold small"
            >Número</label
          >
          <input
            id="numero"
            v-model.trim="novoTutor.numero"
            type="text"
            class="form-control"
            placeholder="Número"
          />
        </div>

        <div class="col-md-6">
          <label
            for="bairro"
            class="form-label fw-semibold small"
            >Bairro</label
          >
          <input
            id="bairro"
            v-model.trim="novoTutor.bairro"
            type="text"
            class="form-control"
            placeholder="Digite o bairro"
          />
        </div>

        <div class="col-md-6">
          <label
            for="cidade"
            class="form-label fw-semibold small"
            >Cidade</label
          >
          <input
            id="cidade"
            v-model.trim="novoTutor.cidade"
            type="text"
            class="form-control"
            placeholder="Digite a cidade"
          />
        </div>

        <div class="col-md-6">
          <label
            for="estado"
            class="form-label fw-semibold small"
            >Estado</label
          >
          <select
            id="estado"
            v-model="novoTutor.estado"
            class="form-select"
          >
            <option value="">Selecione</option>
            <option
              v-for="uf in estados"
              :key="uf"
              :value="uf"
            >
              {{ uf }}
            </option>
          </select>
        </div>

        <div class="col-md-6">
          <label
            for="cep"
            class="form-label fw-semibold small"
            >CEP</label
          >
          <input
            id="cep"
            v-model.trim="novoTutor.cep"
            type="text"
            class="form-control"
            placeholder="00000-000"
          />
        </div>
      </div>

      <div class="d-flex justify-content-end gap-2">
        <RouterLink
          to="/tutores"
          class="btn btn-outline-secondary"
        >
          Cancelar
        </RouterLink>
        <button
          type="submit"
          class="btn btn-primary"
        >
          Cadastrar Tutor
        </button>
      </div>
    </form>
  </div>
</template>