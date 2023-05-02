<template>
  <div class="text-center">
    <v-dialog v-model="dialog" width="auto">
      <template v-slot:activator="{ props }">
        <v-btn color="teal-darken-4" v-bind="props">Rapha</v-btn>
      </template>

      <v-card color="indigo accent-4">
        <v-card-title primary-title class="text-center"> Rapha </v-card-title>
        <v-divider></v-divider>
        <v-card-text>
          <v-table>
            <thead>
              <tr>
                <th>Dia</th>
                <th>Local</th>
                <th>Valor</th>
                <th>Retirar uso</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(user, indice) in cartao" :key="indice">
                <td>{{ user.data }}</td>
                <td>{{ user.local }}</td>
                <td>R$ {{ user.valor }}</td>
                <td>
                  <v-btn
                    outlined
                    color="error"
                    size="small"
                    @click="deleteItem(user.id)"
                    >Excluir</v-btn
                  >
                </td>
              </tr>
            </tbody>
          </v-table>
          <h3 class="text-center mt-2">Total: {{ vlrTotal.toFixed(2) }}</h3>
        </v-card-text>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
import db from "../services/conn";

export default {
  name: "ModalRapha",

  data() {
    return {
      dialog: false,
      cartao: [],
      vlrTotal: 0,
    };
  },

  async created() {
    this.cartao = await this.getData();
    this.vlrTotal = await this.getTotal();
  },

  methods: {
    async getData() {
      const snapshot = await db
        .collection("cartaoRapha")
        .orderBy("data", "asc")
        .get();
      return snapshot.docs.map((doc) => ({
        id: doc.id,
        ...doc.data(),
      }));
    },
    async getTotal() {
      const sum = await this.cartao.reduce(
        (total, item) => total + item.valor,
        0
      );
      return sum;
    },
    async deleteItem(itemId) {
      if (confirm("Tem certeza que deseja excluir esse item?")) {
        await db.collection("cartaoRapha").doc(itemId).delete();
        this.cartao = await this.getData();
        this.vlrTotal = await this.getTotal();
        location.reload();
      }
    },
  },
};
</script>

<style scoped>
#img {
  width: 100px;
  height: 80px;
  cursor: pointer;
  border-radius: 50%;
}
</style>
