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

          <v-text-field v-model="formVal.notify" :rules="[rules.notify]" label="Notify" required></v-text-field>

          <v-text-field v-model="formVal.memberof" :rules="[rules.memberof]" label="Member of"></v-text-field>

          <v-text-field v-model="formVal.remarks" :rules="[rules.remarks]" label="Remarks"></v-text-field>

          <v-text-field v-model="formVal.org" :rules="[rules.org]" label="ORG"></v-text-field>

          <v-row>
            <v-col cols="12" sm="2">
              <v-subheader>default</v-subheader>
            </v-col>
            <v-col cols="12" sm="3">
              <v-text-field v-model="formVal.default.to" :rules="[rules.required]" prefix="to"></v-text-field>
            </v-col>
            <v-col cols="12" sm="3">
              <v-text-field v-model="formVal.default.action" prefix="action"></v-text-field>
            </v-col>
            <v-col cols="12" sm="3">
              <v-text-field v-model="formVal.default.networks" prefix="networks"></v-text-field>
            </v-col>
          </v-row>

          <v-row>
            <v-col class="d-flex" cols="12" sm="2">
              <v-subheader>mp-default</v-subheader>
            </v-col>
            <v-col class="d-flex" cols="12" sm="3">
              <v-text-field v-model="formVal.mp_default.to" :rules="[rules.required]" prefix="to"></v-text-field>
            </v-col>
            <v-col class="d-flex" cols="12" sm="3">
              <v-text-field v-model="formVal.mp_default.action" prefix="action"></v-text-field>
            </v-col>
            <v-col class="d-flex" cols="12" sm="3">
              <v-text-field v-model="formVal.mp_default.networks" prefix="networks"></v-text-field>
            </v-col>
          </v-row>

          <v-row>
            <v-col class="d-flex" cols="12" sm="2">
              <v-subheader>import</v-subheader>
            </v-col>
            <v-col class="d-flex" cols="12" sm="5">
              <v-text-field v-model="formVal.import.protocol_1" dense prefix="protocol"></v-text-field>
            </v-col>
            <v-col class="d-flex" cols="12" sm="5">
              <v-text-field v-model="formVal.import.protocol_1" dense prefix="into"></v-text-field>
            </v-col>
            <template v-for="(slot, index) in formVal.import.import_peering">
              <v-col :key="index + 100" cols="12" sm="2"></v-col>
              <v-col :key="index + 200" class="d-flex" cols="12" sm="4">
                <v-text-field
                  dense
                  :rules="[rules.required]"
                  :prefix="'peering-' + slot.count"
                  v-model="slot.peering"
                ></v-text-field>
              </v-col>
              <v-col :key="index + 300" class="d-flex" cols="12" sm="4">
                <v-text-field dense :prefix="'action-' + slot.count" v-model="slot.action"></v-text-field>
              </v-col>
              <v-col :key="index + 400" cols="12" sm="2">
                <v-btn @click="addAnotherImportPeering()" class="mx-2" small fab dark color="teal">
                  <v-icon dark>mdi-plus</v-icon>
                </v-btn>
                <v-btn
                  @click="deleteImportPeering(index)"
                  v-if="index > 0"
                  class="mx-2"
                  small
                  fab
                  dark
                  color="red"
                >
                  <v-icon dark>delete</v-icon>
                </v-btn>
              </v-col>
            </template>
            <v-col cols="12" sm="2"></v-col>
            <v-col class="d-flex" cols="12" sm="4">
              <v-text-field
                v-model="formVal.export.annonce"
                dense
                prefix="annonce"
                :rules="[rules.required]"
              ></v-text-field>
            </v-col>
          </v-row>

          <v-row>
            <v-col class="d-flex" cols="12" sm="2">
              <v-subheader>export</v-subheader>
            </v-col>
            <v-col class="d-flex" cols="12" sm="5">
              <v-text-field v-model="formVal.export.protocol_1" dense prefix="protocol"></v-text-field>
            </v-col>
            <v-col class="d-flex" cols="12" sm="5">
              <v-text-field v-model="formVal.export.protocol_1" dense prefix="into"></v-text-field>
            </v-col>
            <template v-for="(slot, index) in formVal.export.export_peering">
              <v-col :key="index + 100" cols="12" sm="2"></v-col>
              <v-col :key="index + 200" class="d-flex" cols="12" sm="4">
                <v-text-field
                  dense
                  :rules="[rules.required]"
                  :prefix="'peering-' + slot.count"
                  v-model="slot.peering"
                ></v-text-field>
              </v-col>
              <v-col :key="index + 300" class="d-flex" cols="12" sm="4">
                <v-text-field dense :prefix="'action-' + slot.count" v-model="slot.action"></v-text-field>
              </v-col>
              <v-col :key="index + 400" cols="12" sm="2">
                <v-btn @click="addAnotherExportPeering()" class="mx-2" small fab dark color="teal">
                  <v-icon dark>mdi-plus</v-icon>
                </v-btn>
                <v-btn
                  @click="deleteExportPeering(index)"
                  v-if="index > 0"
                  class="mx-2"
                  small
                  fab
                  dark
                  color="red"
                >
                  <v-icon dark>delete</v-icon>
                </v-btn>
              </v-col>
            </template>
            <v-col cols="12" sm="2"></v-col>
            <v-col class="d-flex" cols="12" sm="4">
              <v-text-field
                v-model="formVal.export.annonce"
                dense
                prefix="annonce"
                :rules="[rules.required]"
              ></v-text-field>
            </v-col>
          </v-row>

          <v-row>
            <v-col class="d-flex" cols="12" sm="2">
              <v-subheader>mp-import</v-subheader>
            </v-col>
            <v-col class="d-flex" cols="12" sm="3">
              <v-text-field v-model="formVal.mp_import.protocol_1" dense prefix="protocol"></v-text-field>
            </v-col>
            <v-col class="d-flex" cols="12" sm="3">
              <v-text-field v-model="formVal.mp_import.protocol_1" dense prefix="into"></v-text-field>
            </v-col>
            <v-col class="d-flex" cols="12" sm="3">
              <v-text-field v-model="formVal.mp_import.afi" dense prefix="afi"></v-text-field>
            </v-col>
            <template v-for="(slot, index) in formVal.mp_import.mp_import_peering">
              <v-col :key="index + 100" cols="12" sm="2"></v-col>
              <v-col :key="index + 200" cols="12" sm="1">
                <v-text-field prefix="from" disabled dense></v-text-field>
              </v-col>
              <v-col :key="index + 300" class="d-flex" cols="12" sm="3">
                <v-text-field
                  dense
                  :rules="[rules.required]"
                  :prefix="'peering-' + slot.count"
                  v-model="slot.peering"
                ></v-text-field>
              </v-col>
              <v-col :key="index + 400" class="d-flex" cols="12" sm="3">
                <v-text-field dense :prefix="'action-' + slot.count" v-model="slot.action"></v-text-field>
              </v-col>
              <v-col :key="index + 500" cols="12" sm="2">
                <v-btn
                  @click="addAnother_mp_ImportPeering()"
                  class="mx-2"
                  small
                  fab
                  dark
                  color="teal"
                >
                  <v-icon dark>mdi-plus</v-icon>
                </v-btn>
              </v-col>
            </template>
            <v-col cols="12" sm="2"></v-col>
            <v-col class="d-flex" cols="12" sm="3">
              <v-text-field
                v-model="formVal.mp_import.filter"
                dense
                prefix="filter"
                :rules="[rules.required]"
              ></v-text-field>
            </v-col>
            <v-col class="d-flex" cols="12" sm="3">
              <v-text-field
                v-model="formVal.mp_import.importexpression_filter"
                dense
                prefix="except"
                :rules="[rules.required]"
              ></v-text-field>
            </v-col>
            <v-col class="d-flex" cols="12" sm="4">
              <v-text-field
                v-model="formVal.mp_import.importexpression"
                dense
                prefix="refine"
                :rules="[rules.required]"
              ></v-text-field>
            </v-col>
          </v-row>

          <v-row>
            <v-col class="d-flex" cols="12" sm="2">
              <v-subheader>mp-export</v-subheader>
            </v-col>
            <v-col class="d-flex" cols="12" sm="3">
              <v-text-field v-model="formVal.mp_export.protocol_1" dense prefix="protocol"></v-text-field>
            </v-col>
            <v-col class="d-flex" cols="12" sm="3">
              <v-text-field v-model="formVal.mp_export.protocol_1" dense prefix="into"></v-text-field>
            </v-col>
            <v-col class="d-flex" cols="12" sm="3">
              <v-text-field v-model="formVal.mp_export.afi" dense prefix="afi"></v-text-field>
            </v-col>
            <template v-for="(slot, index) in formVal.mp_export.mp_export_peering">
              <v-col :key="index + 100" cols="12" sm="2"></v-col>
              <v-col :key="index + 200" cols="12" sm="1">
                <v-text-field prefix="to" disabled dense></v-text-field>
              </v-col>
              <v-col :key="index + 300" class="d-flex" cols="12" sm="3">
                <v-text-field
                  :rules="[rules.required]"
                  dense
                  :prefix="'peering-' + slot.count"
                  v-model="slot.peering"
                ></v-text-field>
              </v-col>
              <v-col :key="index + 400" class="d-flex" cols="12" sm="3">
                <v-text-field dense :prefix="'action-' + slot.count" v-model="slot.action"></v-text-field>
              </v-col>
              <v-col :key="index + 500" cols="12" sm="2">
                <v-btn
                  @click="addAnother_mp_ExportPeering()"
                  class="mx-2"
                  small
                  fab
                  dark
                  color="teal"
                >
                  <v-icon dark>mdi-plus</v-icon>
                </v-btn>
              </v-col>
            </template>
            <v-col cols="12" sm="2"></v-col>
            <v-col class="d-flex" cols="12" sm="6">
              <v-text-field
                v-model="formVal.mp_export.filter"
                dense
                prefix="filter"
                :rules="[rules.required]"
              ></v-text-field>
            </v-col>
          </v-row>

          <v-row>
            <v-col cols="12" sm="6">
              <v-text-field
                v-model="formVal.admin_c"
                :rules="[rules.required, rules.tech_c]"
                label="admin-c"
              ></v-text-field>
            </v-col>
            <v-col cols="12" sm="6">
              <v-text-field
                v-model="formVal.tech_c"
                :rules="[rules.required, rules.tech_c]"
                label="tech-c"
                required
              ></v-text-field>
            </v-col>
          </v-row>

          <v-text-field
            v-model="formVal.mnt_irt"
            :rules="[rules.required, rules.mnt_irt]"
            label="mnt-irt"
            required
          ></v-text-field>

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
            <v-col class="d-flex" cols="12" sm="2">
              <v-subheader>mnt-routes</v-subheader>
            </v-col>
            <v-col class="d-flex" cols="12" sm="10">
              <v-text-field
                dense
                :rules="[rules.required]"
                v-model="formVal.mnt_routes.mnt_name"
                prefix="mnt name"
              ></v-text-field>
            </v-col>
            <template v-for="(slot, index) in formVal.mnt_routes.ip_address">
              <v-col :key="index + 10" cols="12" sm="2"></v-col>
              <v-col :key="index + 20" class="d-flex" cols="12" sm="6">
                <v-text-field dense :rules="[rules.ip]" prefix="IP address" v-model="slot.ip"></v-text-field>
              </v-col>
              <v-col :key="index + 30" class="d-flex" cols="12" sm="2">
                <v-text-field dense prefix="prefix" v-model="slot.prefix"></v-text-field>
              </v-col>
              <v-col :key="index + 40" cols="12" sm="2">
                <v-btn @click="addAnotherIPAddress()" class="mx-2" small fab dark color="teal">
                  <v-icon dark>mdi-plus</v-icon>
                </v-btn>
              </v-col>
            </template>
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

          <v-row>
            <v-col cols="12" sm="8">
              <v-text-field v-model="changedEmail" :rules="[rules.notify]" label="Changed"></v-text-field>
            </v-col>
            <v-col cols="12" sm="4">
              <v-dialog
                ref="dialog"
                v-model="modal"
                :return-value.sync="date"
                persistent
                width="290px"
              >
                <template v-slot:activator="{ on, attrs }">
                  <v-text-field
                    v-model="date"
                    label="Picker in dialog"
                    prepend-icon="event"
                    readonly
                    v-bind="attrs"
                    v-on="on"
                  ></v-text-field>
                </template>
                <v-date-picker v-model="date" scrollable>
                  <v-spacer></v-spacer>
                  <v-btn text color="primary" @click="modal = false">Cancel</v-btn>
                  <v-btn text color="primary" @click="$refs.dialog.save(date)">OK</v-btn>
                </v-date-picker>
              </v-dialog>
            </v-col>
          </v-row>

          <v-btn :disabled="!valid" color="success" class="mr-4 my-2" @click="validate">Submit Form</v-btn>

          <v-btn color="error" class="mr-4 my-2" @click="reset">Reset Form</v-btn>

          <v-btn color="warning" class="my-2" @click="resetValidation">Reset Validation</v-btn>
        </v-form>
      </v-card-text>
    </v-card>
    <v-dialog :persistent="true" v-model="responseDialog" width="500">
      <v-card>
        <v-card-title class="headline success white--text" primary-title dark>Success</v-card-title>
        <v-card-text class="pt-4">{{ responseMessage }}</v-card-text>
        <v-divider></v-divider>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="info" text @click="refreshForm()">Continue</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-snackbar timeout="2000" v-model="snackbar" :color="snackbarColor">
      {{ snackbarText }}
      <template v-slot:action="{ attrs }">
        <v-btn color="white" text v-bind="attrs" @click="snackbar = false">Close</v-btn>
      </template>
    </v-snackbar>
  </v-container>
</template>

<script>
export default {
  data: () => ({
    responseDialog: false,
    snackbar: false,
    snackbarColor: "",
    snackbarText: "",
    responseMessage: "Form has been submitted",

    count: 1,
    valid: true,
    date: new Date().toISOString().substr(0, 10),
    changedEmail: "",
    modal: false,
    aut_num: "",
    mnt_reserved: "",
    as_name_reserved: "",
    mnt_by_reserved: "",
    reserved_words: ["as-", "rs-", "fltr-", "prng-", "irt-"],
    mnt_lowerVal: "",
    as_nameVal: "",
    mnt_byVal: "",
    statusVal: "",
    statusIndex: "",

    formVal: {
      aut_num: "",
      as_name: "",
      descr: "",
      memberof: "",
      status: "",
      notify: "",
      changed: "",
      tech_c: "",
      org: "",
      remarks: "",
      admin_c: "",
      mnt_irt: "",
      mnt_by: "",
      mnt_lower: "",
      mnt_routes: {
        mnt_name: "",
        ip_address: [
          {
            ip: "",
            prefix: "ANY"
          }
        ]
      },
      default: {
        to: "",
        action: "",
        networks: ""
      },
      mp_default: {
        to: "",
        action: "",
        networks: ""
      },
      import: {
        protocol_1: "",
        import_peering: [
          {
            peering: "",
            action: "",
            count: 1
          }
        ],
        annonce: ""
      },
      export: {
        protocol_1: "",
        export_peering: [
          {
            peering: "",
            action: "",
            count: 1
          }
        ],
        annonce: ""
      },
      mp_import: {
        protocol_1: "",
        afi: "",
        mp_import_peering: [
          {
            peering: "",
            action: "",
            count: 1
          }
        ],
        filter: "",
        importexpression_filter: "",
        importexpression: ""
      },
      mp_export: {
        protocol_1: "",
        afi: "",
        mp_export_peering: [
          {
            peering: "",
            action: "",
            count: 1
          }
        ],
        filter: ""
      }
    },
    statusArr: ["o ASSIGNED", "o ASSIGNED-ANYCAST", "o POLICY-RESERVED"],
    rules: {
      required: v => !!v || "Required.",
      aut: v =>
        (!isNaN(parseFloat(v)) && v >= 0 && v <= 4294967295) ||
        "Aut num has to be between 0 and 4294967295",
      notify: v => {
        const pattern = /[a-z0-9!#$%&'*+\/=?^_`{|}~-]+(?:\.[a-z0-9!#$%&'*+\/=?^_`{|}~-]+)*@(?:[a-z0-9](?:[a-z0-9-]*[a-z0-9])?\.)+[a-z0-9](?:[a-z0-9-]*[a-z0-9])?/gm;
        return pattern.test(v) || "Invalid e-mail as defined in RFC 2822.";
      },
      remarks: v => {
        const pattern = /^[\x00-\x7F]*$/;
        return pattern.test(v) || "Should be only ASCII characters";
      },
      tech_c: v => {
        const pattern = /^[a-zA-Z]{2,4}((?!(0))[0-9]{1,5})-[a-zA-Z]{1,9}$/;
        return (
          pattern.test(v) ||
          "From 2 to 4 characters optionally followed by up to 5 digits(First digit must not be 0) and starts with - followed by source name up to 9-character length."
        );
      },
      org: v => {
        const pattern = /^ORG-[a-zA-Z]{2,4}((?!(0))[0-9]{1,5})-[a-zA-Z]{1,9}$/;
        return (
          pattern.test(v) ||
          "Starts with 'ORG-' followed by 2 to 4 characters optionally followed by up to 5 digits( First digit must not be 0) and starts with - followed by source name up to 9-character length."
        );
      },
      mnt_irt: v => {
        const pattern = /^irt-[a-zA-Z0-9-_]+[a-zA-Z\d]$/;
        return (
          pattern.test(v) ||
          "Starts with 'irt-' made up of letters, digits, the character '-', and the character '_', the last character of a name must be a letter or a digit"
        );
      },
      mnt_lower: v => {
        const pattern = /^[a-zA-Z]+[a-zA-Z0-9-_]+[a-zA-Z\d]$/;
        return (
          pattern.test(v) ||
          "Start with a letter, made up of letters, digits, the character '-', and the character '_', the last character of a name must be a letter or a digit"
        );
      },
      memberof: v => {
        const pattern = /^as-[a-zA-Z]+[a-zA-Z0-9-_\s*:\s*\w*]+[a-zA-Z\d]$/;
        return (
          pattern.test(v) ||
          "Start with as-, made up of letters, digits, the character '-', and the character '_', the last character of a name must be a letter or a digit, can also be seperated by colons"
        );
      },
      ip: v => {
        const pattern = /((^\s*((([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])\.){3}([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5]))\s*$)|(^\s*((([0-9A-Fa-f]{1,4}:){7}([0-9A-Fa-f]{1,4}|:))|(([0-9A-Fa-f]{1,4}:){6}(:[0-9A-Fa-f]{1,4}|((25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)(\.(25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)){3})|:))|(([0-9A-Fa-f]{1,4}:){5}(((:[0-9A-Fa-f]{1,4}){1,2})|:((25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)(\.(25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)){3})|:))|(([0-9A-Fa-f]{1,4}:){4}(((:[0-9A-Fa-f]{1,4}){1,3})|((:[0-9A-Fa-f]{1,4})?:((25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)(\.(25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)){3}))|:))|(([0-9A-Fa-f]{1,4}:){3}(((:[0-9A-Fa-f]{1,4}){1,4})|((:[0-9A-Fa-f]{1,4}){0,2}:((25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)(\.(25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)){3}))|:))|(([0-9A-Fa-f]{1,4}:){2}(((:[0-9A-Fa-f]{1,4}){1,5})|((:[0-9A-Fa-f]{1,4}){0,3}:((25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)(\.(25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)){3}))|:))|(([0-9A-Fa-f]{1,4}:){1}(((:[0-9A-Fa-f]{1,4}){1,6})|((:[0-9A-Fa-f]{1,4}){0,4}:((25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)(\.(25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)){3}))|:))|(:(((:[0-9A-Fa-f]{1,4}){1,7})|((:[0-9A-Fa-f]{1,4}){0,5}:((25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)(\.(25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)){3}))|:)))(%.+)?\s*$))/;
        return pattern.test(v) || "Invalid <ipv4-address> or <ipv6-address>";
      }
    }
  }),
  computed: {
    formatDate() {
      var formatDate = new Date(this.date);
      var y = formatDate.getFullYear();
      var m = formatDate.getMonth() + 1;
      var d = formatDate.getDate();
      return "" + y + (m < 10 ? "0" : "") + m + (d < 10 ? "0" : "") + d;
    },
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
    }
  },

  methods: {
    addAnotherExportPeering() {
      this.formVal.export.export_peering.push({
        peering: "",
        action: "",
        count: ++this.count
      });
    },
    deleteExportPeering(index) {
      this.formVal.export.export_peering.splice(
        this.formVal.export.export_peering.indexOf(index),
        1
      );
    },
    addAnotherImportPeering() {
      this.formVal.import.import_peering.push({
        peering: "",
        action: "",
        count: ++this.count
      });
    },
    deleteImportPeering(index) {
      this.formVal.import.import_peering.splice(
        this.formVal.import.import_peering.indexOf(index),
        1
      );
    },
    addAnother_mp_ImportPeering() {
      this.formVal.mp_import.mp_import_peering.push({
        peering: "",
        action: "",
        count: ++this.count
      });
      this.snackbar = true;
      this.snackbarText = "row added";
      this.snackbarColor = "info";
    },
    addAnother_mp_ExportPeering() {
      this.formVal.mp_export.mp_export_peering.push({
        peering: "",
        action: "",
        count: ++this.count
      });
    },
    addAnotherIPAddress() {
      this.formVal.mnt_routes.ip_address.push({
        ip: "",
        prefix: "ANY"
      });
    },
    validate() {
      this.responseDialog = true;
      this.formVal.as_name = this.as_name_reserved + this.as_nameVal;
      this.formVal.mnt_lower = this.mnt_reserved + this.mnt_lowerVal;
      this.formVal.mnt_by = this.mnt_by_reserved + this.mnt_byVal;
      this.formVal.status = this.statusComputed;
      this.formVal.changed = this.changedEmail + " " + this.formatDate;

      if (this.$refs.form.validate()) {
        this.formVal.aut_num = "AS" + this.aut_num;
        console.log(this.formVal);
      }
    },
    reset() {
      this.$refs.form.reset();
    },
    resetValidation() {
      this.$refs.form.resetValidation();
    },
    refreshForm() {
      this.reset();
      this.resetValidation();
      this.responseDialog = false;
    }
  }
};
</script>