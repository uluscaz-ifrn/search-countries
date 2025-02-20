<script setup lang="ts">
import { ref, watch } from 'vue';
import axios from 'axios';
import debounce from 'lodash/debounce';

const country = ref('');
const countryData = ref<any>(null);
const errorMessage = ref('');
const loading = ref(false);

const searchCountry = debounce(async (newCountry: string) => {
  if (!newCountry) {
    countryData.value = null;
    errorMessage.value = '';
    return;
  }

  loading.value = true;
  errorMessage.value = '';

  try {
    const response = await axios.get(`https://restcountries.com/v3.1/name/${newCountry}`);
    countryData.value = response.data[0] || null;
    if (!countryData.value) {
      errorMessage.value = 'País não encontrado';
    }
  } catch (error) {
    console.error('Erro ao buscar país:', error);
    errorMessage.value = 'Erro ao buscar dados. Tente novamente.';
    countryData.value = null;
  } finally {
    loading.value = false;
  }
}, 500);

watch(country, searchCountry);
</script>
<template>
  <v-container>
    <v-text-field v-model="country" label="Digite o nome do país" />
    <div v-if="countryData">
      <h3>{{ countryData.name.common }}</h3>
      <img :src="countryData.flags.svg" alt="Bandeira" width="100" />
      <p>População: {{ countryData.population }}</p>
      <p>Capital: {{ countryData.capital[0] }}</p>
    </div>
  </v-container>
</template>
<style scoped>
  h3 {
    color: #1e88e5;
  }
  .error {
    color: red;
    font-weight: bold;
  }
</style>
