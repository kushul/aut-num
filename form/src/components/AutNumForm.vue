<template>
  <v-container class="my-6">
    <v-card>
      <v-card-title class="headline cyan lighten-3" dark primary-title>Autonomous System Form</v-card-title>
      <v-card-text class="pa-4">
        <v-form ref="form" v-model="valid" lazy-validation>
          <v-text-field prefix="AS" :counter="10" label="aut-num" required></v-text-field>

          <v-text-field label="Notify" required></v-text-field>

          <v-text-field label="Member of"></v-text-field>

          <v-text-field label="Remarks"></v-text-field>

          <v-text-field label="ORG"></v-text-field>

          <v-row>
            <v-col cols="12" sm="6">
              <v-text-field label="admin-c"></v-text-field>
            </v-col>
            <v-col cols="12" sm="6">
              <v-text-field label="tech-c" required></v-text-field>
            </v-col>
          </v-row>

          <v-btn :disabled="!valid" color="success" class="mr-4" @click="validate">Validate</v-btn>

          <v-btn color="error" class="mr-4" @click="reset">Reset Form</v-btn>

          <v-btn color="warning" @click="resetValidation">Reset Validation</v-btn>
        </v-form>
      </v-card-text>
    </v-card>
  </v-container>
</template>

<script>
export default {
  data: () => ({
    valid: true
  }),

  methods: {
    validate() {
      this.$refs.form.validate();
    },
    reset() {
      this.$refs.form.reset();
    },
    resetValidation() {
      this.$refs.form.resetValidation();
    }
  }
};
</script>