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
        @update:model-value="fetchVehicleYears"
      />

      <v-select
        v-model="selectedYear"
        class="mt-4"
        :disabled="!selectedVehicle"
        item-title="text"
        item-value="value"
        :items="years"
        label="Selecione o ano"
      />

      <div class="mt-6 text-center">
        <v-btn
          color="primary"
          :disabled="!selectedYear"
          size="large"
          @click="fetchVehicleDetails"
        >
          Pesquisar
        </v-btn>
      </div>

      <!-- Modal para exibir os detalhes do veículo -->
      <v-dialog v-model="dialog" max-width="500px">
        <v-card v-if="vehicleDetails">
          <v-card-title class="text-h5">
            {{ vehicleDetails.Modelo }}
          </v-card-title>

          <v-card-text>
            <v-list>
              <v-list-item>
                <v-list-item-title>Marca:</v-list-item-title>
                <v-list-item-subtitle>{{ vehicleDetails.Marca }}</v-list-item-subtitle>
              </v-list-item>

              <v-list-item>
                <v-list-item-title>Ano:</v-list-item-title>
                <v-list-item-subtitle>{{ vehicleDetails.AnoModelo }}</v-list-item-subtitle>
              </v-list-item>

              <v-list-item>
                <v-list-item-title>Combustível:</v-list-item-title>
                <v-list-item-subtitle>{{ vehicleDetails.Combustivel }}</v-list-item-subtitle>
              </v-list-item>

              <v-list-item>
                <v-list-item-title>Código FIPE:</v-list-item-title>
                <v-list-item-subtitle>{{ vehicleDetails.CodigoFipe }}</v-list-item-subtitle>
              </v-list-item>

              <v-list-item>
                <v-list-item-title>Mês de referência:</v-list-item-title>
                <v-list-item-subtitle>{{ vehicleDetails.MesReferencia }}</v-list-item-subtitle>
              </v-list-item>

              <v-list-item>
                <v-list-item-title class="text-h6 font-weight-bold">Valor:</v-list-item-title>
                <v-list-item-subtitle class="text-h6 font-weight-bold text-success">
                  {{ vehicleDetails.Valor }}
                </v-list-item-subtitle>
              </v-list-item>
            </v-list>
          </v-card-text>

          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="primary" text @click="dialog = false">
              Fechar
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

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
        years: [],
        selectedBrand: null,
        selectedVehicle: null,
        selectedYear: null,
        dialog: false,
        vehicleDetails: null,
        loading: false,
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
        // Também limpa os anos
        this.selectedYear = null;
        this.years = [];

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

      async fetchVehicleYears () {
        // Limpa a seleção de ano quando o veículo muda
        this.selectedYear = null;
        this.years = [];

        if (!this.selectedBrand || !this.selectedVehicle) return;

        try {
          const response = await axios.get(
            `https://parallelum.com.br/fipe/api/v1/carros/marcas/${this.selectedBrand}/modelos/${this.selectedVehicle}/anos`
          );

          this.years = response.data.map(year => ({
            text: year.nome,
            value: year.codigo,
          }));
        } catch (error) {
          console.error('Erro ao buscar anos do veículo:', error);
        }
      },

      async fetchVehicleDetails () {
        this.loading = true;

        try {
          const response = await axios.get(
            `https://parallelum.com.br/fipe/api/v1/carros/marcas/${this.selectedBrand}/modelos/${this.selectedVehicle}/anos/${this.selectedYear}`
          );

          this.vehicleDetails = response.data;
          this.dialog = true;
        } catch (error) {
          console.error('Erro ao buscar detalhes do veículo:', error);
          alert('Não foi possível obter os detalhes do veículo. Por favor, tente novamente.');
        } finally {
          this.loading = false;
        }
      }
    },
  };

</script>
