<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="10">
      <nuxt-link to="/">Home</nuxt-link>
      <v-select
        v-model="status"
        :items="statuses"
        label="Статус"
        dense
      ></v-select>
      <v-data-table :headers="headers" :items="items" class="elevation-1">
        <template v-slot:header.name="{ header }">
          {{ header.text.toUpperCase() }}
        </template>
      </v-data-table>
    </v-col>
  </v-row>
</template>

<script>
export default {
  data: () => ({
    headers: [
      {
        text: "Имя",
        align: "start",
        value: "name",
      },
      { text: "Телефон", value: "number" },
      { text: "Дата приезда", value: "dateStart" },
      { text: "Дата выезда", value: "dateFinish" },
      { text: "Статус", value: "status" },
    ],
    items: [],
    status: null,
    statuses: [],
  }),
  watch: {
    status() {
      this.filterTable();
    },
  },
  methods: {
    updateTable(val) {
      const res = val.val();
      if(!res){
        this.items = []
        return
      }
      this.items = Object.keys(res).map((item) => {
        return {
          key: item,
          ...res[item],
        };
      });
    },
    async filterTable() {
      const messageRef = this.$fire.database
        .ref("/users")
        .orderByChild("status")
        .equalTo(this.status);
      const val = await messageRef.once("value");

      this.updateTable(val);
    },
  },
  async mounted() {
    const messageRef = this.$fire.database.ref("users");
    const statuseseRef = this.$fire.database.ref("statuses");
    try {
      const val = await messageRef.once("value");
      this.updateTable(val);
      const statuses = await statuseseRef.once("value");
      this.statuses = Object.keys(statuses.val());
    } catch (e) {
      alert(e);
      return;
    }
  },
  // beforedestroy() {
  //   const messageRef = this.$fire.database.ref("users");

  //   messageRef.off("value", this.updateTable);
  // },
};
</script>
