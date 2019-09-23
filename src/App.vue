<template>
  <v-app>
    <v-app-bar app>
      <v-toolbar-title class="headline text-uppercase">
        <span>FILMS</span>
        <span class="font-weight-light"> Search</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
    </v-app-bar>

    <v-content>
      <Films />
    </v-content>
  </v-app>
</template>

<script>
import Films from "./components/Films";

export default {
  name: "App",
  components: {
    Films
  },
  data: () => ({
    //
  })
};
</script>
