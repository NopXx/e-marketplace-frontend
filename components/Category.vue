<template>
  <v-item-group mandatory class="mt-n1">
    <v-container>
      <v-row v-if="loadshow" justify="center">
        <v-col cols="12" md="6">
          <v-skeleton-loader
            v-bind="attrs"
            type="article, actions"
          ></v-skeleton-loader>
        </v-col>
      </v-row>
      <v-row v-else justify="center" class="space">
        <v-col cols="12" xs="12" sm="4" md="2" @click="selecttype(0)">
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
                          src="https://res.cloudinary.com/dqolakmsp/image/upload/v1676308580/assets/e-shop-no-image_uuubeq.png"
                          max-height="100"
                          max-width="80"
                          contain
                        >
                          <template #placeholder>
                            <v-row
                              class="fill-height ma-0"
                              align="center"
                              justify="center"
                            >
                              <v-progress-circular
                                indeterminate
                                color="grey lighten-5"
                              ></v-progress-circular>
                            </v-row>
                          </template>
                        </v-img>
                      </div>
                      <v-list-item-title class="mt-4 text-h6" color="primary"
                        >ทั้งหมด</v-list-item-title
                      >
                    </v-list-item-content>
                  </v-list-item>
                </v-col>
              </v-row>
            </v-card>
          </v-item>
        </v-col>
        <v-col
          v-for="(cetegory, i) in type"
          :key="i"
          cols="12"
          xs="12"
          sm="4"
          md="2"
          @click="selecttype(cetegory.product_type_id)"
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
                          v-if="cetegory.image !== null"
                          :src="cetegory.image"
                          max-height="100"
                          max-width="80"
                          contain
                        >
                          <template #placeholder>
                            <v-row
                              class="fill-height ma-0"
                              align="center"
                              justify="center"
                            >
                              <v-progress-circular
                                indeterminate
                                color="grey lighten-5"
                              ></v-progress-circular>
                            </v-row>
                          </template>
                        </v-img>
                        <v-img
                          v-else
                          src="https://res.cloudinary.com/dqolakmsp/image/upload/v1676308580/assets/e-shop-no-image_uuubeq.png"
                          max-height="100"
                          max-width="80"
                          contain
                        >
                          <template #placeholder>
                            <v-row
                              class="fill-height ma-0"
                              align="center"
                              justify="center"
                            >
                              <v-progress-circular
                                indeterminate
                                color="grey lighten-5"
                              ></v-progress-circular>
                            </v-row>
                          </template>
                        </v-img>
                      </div>
                      <v-list-item-title class="mt-4 text-h6" color="primary">{{
                        cetegory.product_type_name
                      }}</v-list-item-title>
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
    loadshow: false,
    selectedItem: 0,
  }),
  async created() {
    await this.getProductType()
  },
  methods: {
    async getProductType() {
      this.loadshow = true
      try {
        const respo = await this.$axios.get('/product-type')
        // eslint-disable-next-line no-console
        setTimeout(() => {
          this.type = respo.data
          this.loadshow = false
        }, respo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.error(e)
      }
    },
    selecttype(id) {
      return this.$emit('selecttype', id)
    },
  },
}
</script>

<style>
</style>