<template>
  <v-container class="pb-0">
    <v-row class="mb-0 mb-md-10 pt-10 mt-16 mt-sm-0">
      <v-col class="">
        <v-form ref="form" class=" mt-md-5 register " id="form">
          <v-row class="px-10 mt-5">
            <v-col class="mt-sm-16 mt-0 col-12 col-sm-6">
              <v-text-field
                :rules="rules"
                label="Your Name"
                v-model="usersData.name"
              ></v-text-field>
            </v-col>
            <v-col class="mt-sm-16 mt-0 col-12 col-sm-6">
              <v-select
                :rules="rules"
                :items="storeDepartments"
                label="Select Department"
                v-model="usersData.department"
              ></v-select>
            </v-col>
          </v-row>
          <v-row class="px-10">
            <v-col class="col-12 col-sm-6">
              <v-text-field
                :rules="rules"
                label="Product Code (ex.AB1234)"
                v-model="usersData.productCode"
              ></v-text-field>
            </v-col>
            <v-col class="col-12 col-sm-6">
              <v-text-field
                :rules="rules"
                label="Genral Info (ex. product name)"
                v-model="usersData.generalInfo"
              ></v-text-field>
            </v-col>
          </v-row>
          <v-row class="d-flex px-10 mt-sm-5 mt-0 ">
            <v-col class="col-sm-6 col-12">
              <v-textarea
                :rules="rules"
                filled
                label="Details"
                placeholder="Be as much descriptive as you can in order to help other colleagues search for it in the future!"
                v-model="usersData.details"
              ></v-textarea>
            </v-col>
            <v-col>
              <v-row class="d-flex flex-column">
                <v-col class="d-flex align-start">
                  <v-select
                    :rules="rules"
                    :items="status"
                    label="Status"
                    class=""
                    v-model="usersData.searchStatus"
                  ></v-select>
                </v-col>
                <v-col>
                  <v-dialog
                    ref="dialog"
                    v-model="modal"
                    :return-value.sync="date"
                    persistent
                    width="290px"
                  >
                    <template v-slot:activator="{ on, attrs }">
                      <v-text-field
                        :rules="rules"
                        v-model="usersData.date"
                        label="Select a date"
                        prepend-icon="mdi-calendar"
                        readonly
                        v-bind="attrs"
                        v-on="on"
                      ></v-text-field>
                    </template>
                    <v-date-picker v-model="usersData.date" scrollable>
                      <v-spacer></v-spacer>
                      <v-btn text color="primary" @click="modal = false">
                        Cancel
                      </v-btn>
                      <v-btn
                        text
                        color="primary"
                        @click="$refs.dialog.save(date)"
                      >
                        OK
                      </v-btn>
                    </v-date-picker>
                  </v-dialog>
                </v-col>
              </v-row>
            </v-col>
          </v-row>
          <v-row class="px-10 mt-5">
            <v-col class="d-flex justify-center align-center">
              <v-btn dark color="#333333" @click="sendInfo">Add</v-btn>
            </v-col>
          </v-row>
        </v-form>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import axios from 'axios';
export default {
  data() {
    return {
      rules: [
        value => {
          if (!value) {
            return 'Required field!';
          } else {
            return true;
          }
        }
      ],
      date: new Date(Date.now() - new Date().getTimezoneOffset() * 60000)
        .toISOString()
        .substr(0, 10),
      menu: false,
      modal: false,

      storeDepartments: ['Shoe', 'Apparel', 'Accessories/Other'],
      status: [
        'Pending',
        'Checked/Wrong sale',
        'Checked/Stolen',
        'Checked/Damaged'
      ],
      usersData: {
        active: true
      }
    };
  },
  methods: {
    modifyData() {
      const code = this.usersData.productCode?.toUpperCase();
      const info = this.usersData.generalInfo?.toLowerCase();
      if (!this.usersData.productCode && !this.usersData.generalInfo) return;
      this.usersData.productCode = code;
      this.usersData.generalInfo = info;
    },
    sendInfo() {
      if (Object.values(this.usersData).length !== 8) {
        this.$refs.form.reset();
        const payload = 'All fields must be completed!';
        this.$store.commit('showSnackbar', payload);
        this.usersData = {
          active: true
        };
        return;
      }

      const url =
        'https://in-store-operations-app-default-rtdb.europe-west1.firebasedatabase.app/surveys.json';
      this.modifyData();
      axios
        .post(url, this.usersData)
        .then(res => {
          if (res.status === 200) {
            const payload = 'New Product added!';
            this.$store.commit('showSnackbar', payload);
            this.$refs.form.reset();
            this.usersData = {
              active: true
            };
          }
        })
        .catch(err => console.log(err));
    }
  },
  mounted() {},
  updated() {}
};
</script>

<style scoped lang="scss">
@import '~vuetify/src/components/VBtn/_variables.scss';
@import '/src/sass/variables.scss';

.register {
  height: 100%;
  background-color: #f5f5f5;
}
</style>
