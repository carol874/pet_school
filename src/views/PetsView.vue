<script setup>
import { onMounted, ref } from 'vue';
import { RouterLink } from 'vue-router';
const API_URL = 'http://localhost:3000';
const pets = ref([]);
const tutores = ref([]);
const loading = ref(true);
const MensagemErro = ref(''); /* usei essa variável reativa caso a API não consiga consultar os pets vai aparecer essa mensagem*/

async function carregarPets() {
       //Prof aqui eu só renomeei a função carregarDados, já que elas estavam fazendo a mesma tarefa
  try {
    const respostaPets = await fetch(`${API_URL}/pets`);
    if (!respostaPets.ok) { //se respostaPets.ok for = false ele vai exibir a mensagem 'Erro ao consultar os pets'
    // como repostaPets.ok deu false eu pedi pra ele executar um erro com o trhow new error 
      throw new Error('Erro ao consultar os pets');
    }
    // aqui se a respostaPets.ok for = true ele vai pegar os dados dos pets e usando o json guardar os dados deles na ref pets que criamos
    pets.value = await respostaPets.json();
    console.log('Pets:', pets.value);

    const respostaTutores = await fetch(`${API_URL}/tutores`);
     tutores.value = await respostaTutores.json();
    console.log('Tutores:', tutores.value);
    loading.value = false;

  } catch (error) { // se o throw acontecer ele pede para executar o catch que vai exibir a nossa mensagem de erro por meio da ref que eu criei anteriormente
    MensagemErro.value = 'Erro ao carregar os dados dos pets ):';
    loading.value = false; // se executar a nossa mensagem de erro, a variável loading vai ser = false
  }
}

function nomeDoTutor(tutorId) {

  for (const tutor of tutores.value) {
    // tutor.id == tutorId
    if (tutor.id === tutorId) {
      return tutor.nome;
    }
  }
  return 'Tutor Não Encontrado!';

}

// Recebe o tutorId do pet e procura o tutor correspondente.
onMounted(carregarPets);
</script>

<template>
  <div>
    <header class="mb-4">
      <h1 class="text-2xl font-bold">Listagem de Pets</h1>
      <p class="text-body-secondary mb-0">
        Listagem dos Pets cadastrados no sistema.
      </p>
    </header>

    <RouterLink
      class="btn btn-primary"
      :to="{ name: 'addPet' }"
    >
      Adicionar Pet
    </RouterLink>

    <table class="table table-striped table-hover">
      <thead>
        <tr>
          <th>ID</th>
          <th>Nome</th>
          <th>Especie</th>
          <th>Tutor</th>
        </tr>
      </thead>

      <tbody>
        <tr
          v-for="pet in pets"
          :key="pet.id"
        >
          <td>{{ pet.id }}</td>
          <td>{{ pet.nome }}</td>
          <td>{{ pet.especie }}</td>
          <td>{{ nomeDoTutor(pet.tutorId) }}</td>
        </tr>
      </tbody>
    </table>
  </div>

  <div v-if="loading"> <!-- aqui eu usei a condicional v-if, caso a variável loading seja = true ele vai me exibir a resposta de carregamento -->
  Carregando Pets hihi
</div>

<div v-else-if="MensagemErro"> <!-- aqui quase a mesma coisa se a primeira condição que no caso o loading for false, ele vai executar esse condição que é a mensagem de erro -->
  {{ MensagemErro }}
</div>

<table 
  v-else
  class="table table-striped table-hover" 
> <!-- se as duas primeiras forem = false ele vai mostar a tabela com os dados dos pets-->
</table>
</template>