<template>
  <v-item-group mandatory class="mt-n1">
    <v-container>
      <v-row justify="center" class="space">
        <v-col
          v-for="(cetegory, i) in type.data"
          :key="i"
          cols="12"
          xs="12"
          sm="4"
          md="2"
        >
          <v-item v-slot="{ active, toggle }">
            <v-card
              :class="active ? 'primary' : 'borderout'"
              class="d-flex align-center rounded-lg mx-2"
              height="180"
              flat
              @click="toggle"
            >
              <v-row>
                <v-col cols="12" sm="12">
                  <v-list-item three-line class="text-center">
                    <v-list-item-content>
                      <div align="center" justify="center">
                        <v-img
                          :src="require(`~/assets/img/01.png`)"
                          max-height="80"
                          max-width="80"
                          contain
                        ></v-img>
                      </div>
                      <v-list-item-title
                        :class="active ? '' : 'black--text'"
                        class="mt-4 text-h6"
                        color="primary"
                        >{{ cetegory.product_type_name }}</v-list-item-title
                      >
                    </v-list-item-content>
                  </v-list-item>
                </v-col>
              </v-row>
            </v-card>
          </v-item>
        </v-col>
      </v-row>
    </v-container>
  </v-item-group>
</template>

<script>
export default {
  name: 'CategoryComponent',
  data: () => ({
    type: [],
  }),
  created() {
    this.getProductType()
  },
  methods: {
    async getProductType() {
      try {
        const respo = await this.$axios.get('/product-type')
        // eslint-disable-next-line no-console
        this.type = respo.data
      } catch (e) {
        // eslint-disable-next-line no-console
        console.error(e)
      }
    },
  },
}
</script>

<style>
</style>