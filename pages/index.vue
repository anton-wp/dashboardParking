<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <nuxt-link to="/dashboard">dashboard</nuxt-link>
      <v-text-field
        label="Имя"
        hide-details="auto"
        v-model="data.name"
      ></v-text-field>
      <!-- <VuePhoneNumberInput v-model="data.number"/> -->
      <v-text-field label="Номер" v-model="data.number"></v-text-field>

      <v-col cols="12" lg="6">
        <v-text-field
          v-model="dateRangeText"
          label="Date range"
          prepend-icon="mdi-calendar"
          readonly
        ></v-text-field>

        <v-date-picker v-model="data.dates" range></v-date-picker>
      </v-col>

      <!-- <v-col cols="12" lg="6">
        <v-menu v-model="menu2" :close-on-content-click="false" max-width="290">
          <template v-slot:activator="{ on, attrs }">
            <v-text-field
              :value="computedDateFormattedDatefns"
              clearable
              label="Formatted with datefns"
              readonly
              v-bind="attrs"
              v-on="on"
              @click:clear="data.dateFinish = null"
            ></v-text-field>
          </template>
          <v-date-picker
            v-model="data.dateFinish"
            @change="menu2 = false"
          ></v-date-picker>
        </v-menu>
      </v-col> -->
      <v-btn depressed color="primary" @click="writeToRealtimeDb">
        Primary
      </v-btn>
    </v-col>
  </v-row>
</template>
<script>
import moment from "moment";
import { format, parseISO } from "date-fns";
export default {
  data: () => ({
    data: {
      name: "",
      number: "",
      dates: [],
      status: "new",
    },
    menu1: false,
    menu2: false,
  }),
  computed: {
    dateRangeText() {
      return this.data.dates.join(" ~ ");
    },
  },
  methods: {
    async writeToRealtimeDb() {
      const messageRef = this.$fire.database.ref("users");
      console.log(messageRef);
      try {
        const res = await messageRef.push(this.data);
      } catch (e) {
        alert(e);
        return;
      }
      alert("Success.");
    },
  },
};
</script>
