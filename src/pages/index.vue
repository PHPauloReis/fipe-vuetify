<template>
  <v-layout>
    <v-container>

      <h1 class="mb-5">Bem-vindo à FIPE UNIME</h1>

      <v-select
        item-title="text"
        item-value="value"
        :items="items"
        label="Item 2"
      />

    </v-container>
  </v-layout>
</template>

<script>

  import { ref } from 'vue';

  export default {
    setup () {
      const items = ref([
        { text: 'Item 1', value: 1 },
        { text: 'Item 2', value: 2 },
        { text: 'Item 3', value: 3 },
      ]);

      return {
        items,
      };
    },
  };

</script>
