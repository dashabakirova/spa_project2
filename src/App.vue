<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center">
        <h1>Справочник объектов</h1>
      </div>
      <v-spacer></v-spacer>
    </v-app-bar>

    <v-main>
      <SprDataTable/>
    </v-main>
  </v-app>
</template>

<script>
import SprDataTable from './components/SprDataTable'

export default {
  name: "App",

  components: {
    SprDataTable,
  },

  data: () => ({
    //
  }),
};
</script>
