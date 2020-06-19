<template>
  <v-container class="my-6">
    <v-card>
      <v-card-title class="headline cyan lighten-3" dark primary-title>Autonomous System Form</v-card-title>
      <v-card-text class="pa-4">
        <v-form ref="form" v-model="valid" lazy-validation>
          <v-text-field
            v-model="aut_num"
            prefix="AS"
            :counter="10"
            :rules="[rules.required, rules.aut]"
            label="aut-num"
            required
          ></v-text-field>

          <v-row>
            <v-col class="d-flex" cols="12" sm="3">
              <v-select
                small-chips
                :items="reserved_words"
                v-model="as_name_reserved"
                label="Reserved Names"
                :rules="[rules.required]"
                required
              ></v-select>
            </v-col>
            <v-col class="d-flex" cols="12" sm="9">
              <v-text-field
                v-model="as_nameVal"
                :rules="[rules.required,rules.mnt_lower]"
                label="as-name"
              ></v-text-field>
            </v-col>
          </v-row>

          <v-text-field v-model="formVal.notify" :rules="[rules.notify]" label="Notify" required></v-text-field>

          <v-text-field v-model="formVal.remarks" :rules="[rules.remarks]" label="Remarks"></v-text-field>

          <v-text-field v-model="formVal.org" :rules="[rules.org]" label="ORG"></v-text-field>

          <v-text-field
            v-model="formVal.mnt_irt"
            :rules="[rules.required, rules.mnt_irt]"
            label="mnt-irt"
            required
          ></v-text-field>

          <v-row>
            <v-col cols="12" sm="6">
              <v-text-field label="admin-c"></v-text-field>
            </v-col>
            <v-col cols="12" sm="6">
              <v-text-field label="tech-c" required></v-text-field>
            </v-col>
          </v-row>

          <v-row>
            <v-col sm="6" cols="12">
              <v-text-field
                v-model="formVal.descr"
                :rules="[rules.required,rules.remarks]"
                label="descr"
              ></v-text-field>
            </v-col>
            <v-col sm="6" cols="12">
              <v-slider
                v-model="statusIndex"
                :tick-labels="statusArr"
                thumb-label="always"
                min="0"
                max="2"
                ticks="always"
                tick-size="4"
                color="teal"
              >
                <template v-slot:thumb-label>
                  <v-icon dark>place</v-icon>
                </template>
              </v-slider>
            </v-col>
          </v-row>

          <v-row>
            <v-col class="d-flex" cols="12" sm="3">
              <v-select
                small-chips
                :items="reserved_words"
                v-model="mnt_reserved"
                label="Reserved Names"
                :error="false"
              ></v-select>
            </v-col>
            <v-col class="d-flex" cols="12" sm="9">
              <v-text-field v-model="mnt_lowerVal" :rules="[rules.mnt_lower]" label="mnt-lower"></v-text-field>
            </v-col>
          </v-row>

          <v-row>
            <v-col class="d-flex" cols="12" sm="3">
              <v-select
                small-chips
                :items="reserved_words"
                v-model="mnt_by_reserved"
                :rules="[rules.required]"
                label="Reserved Names"
                required
              ></v-select>
            </v-col>
            <v-col class="d-flex" cols="12" sm="9">
              <v-text-field
                v-model="mnt_byVal"
                :rules="[rules.required,rules.mnt_lower]"
                label="mnt-by"
              ></v-text-field>
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
    valid: true,
    aut_num: "",
    statusVal: "",
    statusIndex: "",
    formVal: {
      aut_num: "",
      descr: "",
      as_name: "",
      mnt_lower: "",
      mnt_by: "",
      mnt_irt: "",
      org: "",
      notify: "",
      status: ""
    },
    as_name_reserved: "",
    mnt_reserved: "",
    mnt_by_reserved: "",
    statusArr: ["o ASSIGNED", "o ASSIGNED-ANYCAST", "o POLICY-RESERVED"],
    as_nameVal: "",
    mnt_lowerVal: "",
    mnt_byVal: "",
    reserved_words: ["as-", "rs-", "fltr-", "prng-", "irt-"],
    rules: {
      required: v => !!v || "Required.",
      notify: v => {
        const pattern = /[a-z0-9!#$%&'*+\/=?^_`{|}~-]+(?:\.[a-z0-9!#$%&'*+\/=?^_`{|}~-]+)*@(?:[a-z0-9](?:[a-z0-9-]*[a-z0-9])?\.)+[a-z0-9](?:[a-z0-9-]*[a-z0-9])?/gm;
        return pattern.test(v) || "Invalid e-mail as defined in RFC 2822.";
      },
      aut: v =>
        (!isNaN(parseFloat(v)) && v >= 0 && v <= 4294967295) ||
        "Aut num has to be between 0 and 4294967295",
      mnt_irt: v => {
        const pattern = /^irt-[a-zA-Z0-9-_]+[a-zA-Z\d]$/;
        return (
          pattern.test(v) ||
          "Starts with 'irt-' made up of letters, digits, the character '-', and the character '_', the last character of a name must be a letter or a digit"
        );
      },
      org: v => {
        const pattern = /^ORG-[a-zA-Z]{2,4}((?!(0))[0-9]{1,5})-[a-zA-Z]{1,9}$/;
        return (
          pattern.test(v) ||
          "Starts with 'ORG-' followed by 2 to 4 characters optionally followed by up to 5 digits( First digit must not be 0) and starts with - followed by source name up to 9-character length."
        );
      },
      mnt_lower: v => {
        const pattern = /^[a-zA-Z]+[a-zA-Z0-9-_]+[a-zA-Z\d]$/;
        return (
          pattern.test(v) ||
          "Start with a letter, made up of letters, digits, the character '-', and the character '_', the last character of a name must be a letter or a digit"
        );
      },
      remarks: v => {
        const pattern = /^[\x00-\x7F]*$/;
        return pattern.test(v) || "Should be only ASCII characters";
      }
    }
  }),

  statusComputed() {
    let index = this.statusIndex;
    let statusVal = this.statusVal;
    switch (index) {
      case 0:
        statusVal = "o ASSIGNED";
        break;
      case 1:
        statusVal = "o ASSIGNED-ANYCAST";
        break;
      case 2:
        statusVal = "o POLICY-RESERVED";
        break;
    }
    return statusVal;
  },

  methods: {
    validate() {
      this.formVal.aut_num = "AS" + this.aut_num;
      this.formVal.as_name = this.as_name_reserved + this.as_nameVal;
      this.formVal.mnt_lower = this.mnt_reserved + this.mnt_lowerVal;
      this.formVal.mnt_by = this.mnt_by_reserved + this.mnt_byVal;
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