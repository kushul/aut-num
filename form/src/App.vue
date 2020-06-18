<template>
  <v-app>
    <Header />
    <v-main>
      <HelloWorld />
    </v-main>
  </v-app>
</template>

<script>
import Header from "./components/shared/Header";
import HelloWorld from "./components/HelloWorld";

export default {
  name: "App",

  components: {
    Header,
    HelloWorld
  },

  data: () => ({
    //
  })
};
</script>
