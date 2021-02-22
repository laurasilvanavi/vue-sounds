<template>
  <v-app>
    <v-app-bar justify-center app color="teal" class="justify-center" dark>
      <v-container>
        <v-row no-gutters>
          <v-col cols="12" sm="4"> </v-col>
          <v-col cols="12" sm="4">
            <h2>
              MY INSTRUMENT 🎹
            </h2>
          </v-col>
        </v-row>
      </v-container>
    </v-app-bar>

    <v-main>
      <Instrument />
    </v-main>
  </v-app>
</template>

<script>
import Instrument from "./components/Instrument";

export default {
  name: "App",
  components: {
    Instrument,
  },
};
</script>
<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin-top: 150px;
}
</style>
