<template>
  <v-app>
    <Header />
    <v-main>
      <AutNumForm />
    </v-main>
    <Footer />
  </v-app>
</template>

<script>
import Header from "./components/shared/Header";
import Footer from "./components/shared/Footer";
import AutNumForm from "./components/AutNumForm";

export default {
  name: "App",

  components: {
    Header,
    AutNumForm,
    Footer
  },

  data: () => ({
    //
  })
};
</script>
