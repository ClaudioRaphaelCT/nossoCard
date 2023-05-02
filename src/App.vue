<template>
  <v-app id="initial">
    <div class="title">
      <h1 class="text-center" id="nomes">Rapha e Rhai</h1>
    </div>

    <v-container grid-list-xs class="d-flex justify-center">
      <ModalInserir></ModalInserir>
      <ModalRhai class="mr-3 ml-3"></ModalRhai>
      <ModalRapha></ModalRapha>
    </v-container>
    <footer>
      <div id="total">
        <h3 class="text-center">Valor total do Cartão: R$ {{ total }}</h3>
      </div>
    </footer>
  </v-app>
</template>

<script>
import ModalRhai from "./components/ModalRhai.vue";
import ModalRapha from "./components/ModalRapha.vue";
import ModalInserir from "./components/ModalInserir.vue";
import db from "./services/conn";
export default {
  name: "App",
  components: {
    ModalRhai,
    ModalInserir,
    ModalRapha,
  },
  data() {
    return {
      cartaoRhai: [],
      cartaoRapha: [],
      valorTotalRhai: 0,
      valorTotalRapha: 0,
      total: 0,
    };
  },

  async created() {
    this.cartaoRhai = await this.getDataRhai();
    this.valorTotalRhai = await this.getTotalRhai();
    this.cartaoRapha = await this.getDataRapha();
    this.valorTotalRapha = await this.getTotalRapha();
    this.total = this.valorTotalRapha + this.valorTotalRhai;
  },

  methods: {
    async getDataRhai() {
      const snapshot = await db
        .collection("cartaoRhai")
        .orderBy("data", "asc")
        .get();
      return snapshot.docs.map((doc) => ({
        id: doc.id,
        ...doc.data(),
      }));
    },

    async getTotalRhai() {
      const sum = await this.cartaoRhai.reduce(
        (total, item) => total + item.valor,
        0
      );
      return sum;
    },
    async getDataRapha() {
      const snapshot = await db
        .collection("cartaoRapha")
        .orderBy("data", "asc")
        .get();
      return snapshot.docs.map((doc) => ({
        id: doc.id,
        ...doc.data(),
      }));
    },
    async getTotalRapha() {
      const sum = await this.cartaoRapha.reduce(
        (total, item) => total + item.valor,
        0
      );
      return sum;
    },
  },
};
</script>

<style>
#total {
  background-color: white;
  margin-top: 43%;
}

#nomes {
  color: whitesmoke;
}

#initial {
  background: none;
}
</style>
