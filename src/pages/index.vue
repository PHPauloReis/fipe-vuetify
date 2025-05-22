<template>
  <v-layout>
    <v-container>

      <h1 class="mb-5">Bem-vindo à FIPE UNIME</h1>

      <v-select
        v-model="selectedBrand"
        item-title="text"
        item-value="value"
        :items="items"
        label="Selecione uma marca"
        @update:model-value="fetchCarModels"
      />

      <v-select
        v-model="selectedVehicle"
        class="mt-4"
        :disabled="!selectedBrand"
        item-title="text"
        item-value="value"
        :items="vehicles"
        label="Selecione um veículo"
      />

    </v-container>
  </v-layout>
</template>

<script>

  import axios from 'axios';

  export default {
    data () {
      return {
        items: [],
        vehicles: [],
        selectedBrand: null,
        selectedVehicle: null,
      };
    },

    mounted () {
      this.fetchCarBrands();
    },

    methods: {
      async fetchCarBrands () {
        try {
          const response = await axios.get('https://parallelum.com.br/fipe/api/v1/carros/marcas');
          this.items = response.data.map(brand => ({
            text: brand.nome,
            value: brand.codigo,
          }));
        } catch (error) {
          console.error('Erro ao buscar marcas de carros:', error);
        }
      },

      async fetchCarModels () {
        // Limpa a seleção de veículo quando a marca muda
        this.selectedVehicle = null;
        this.vehicles = [];

        if (!this.selectedBrand) return;

        try {
          const response = await axios.get(
            `https://parallelum.com.br/fipe/api/v1/carros/marcas/${this.selectedBrand}/modelos`
          );

          this.vehicles = response.data.modelos.map(model => ({
            text: model.nome,
            value: model.codigo,
          }));
        } catch (error) {
          console.error('Erro ao buscar modelos de veículos:', error);
        }
      },
    },
  };

</script>
