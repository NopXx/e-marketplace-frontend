<template>
  <v-row>
    <v-col cols="2">
      <v-sheet rounded="lg" outlined>
        <v-list-item class="text-center">
          <v-list-item-content>
            <v-avatar size="128">
              <img src="https://cdn.vuetifyjs.com/images/john.jpg" alt="John" />
            </v-avatar>
            <v-list-item-title class="text-h4">{{
              store.store_name
            }}</v-list-item-title>
            <v-list-item-subtitle>{{
              store.store_username
            }}</v-list-item-subtitle>
          </v-list-item-content>
        </v-list-item>
        <v-divider class="my-2"></v-divider>

        <v-list rounded>
          <v-list-item-title>Admin Store</v-list-item-title>
          <v-list-item-group v-model="selectedItem" color="primary">
            <v-list-item v-for="(item, i) in items" :key="i">
              <v-list-item-icon>
                <v-icon>{{ item.icon }}</v-icon>
              </v-list-item-icon>
              <v-list-item-content>
                <v-list-item-title>{{ item.text }}</v-list-item-title>
              </v-list-item-content>
            </v-list-item>
          </v-list-item-group>
        </v-list>
      </v-sheet>
    </v-col>

    <v-col>
      <v-sheet min-height="70vh" rounded="lg" outlined>
        <v-row v-if="selectedItem === 0" class="ma-2">
          <v-col cols="2">
            <v-card elevation="2">
              <v-card-title> จำนวนสินค้าทั้งหมด </v-card-title>
              <v-card-text class="text-h5 text-center">
                {{ product.length }}
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
        <v-row v-else>
            <Product_Add />
        </v-row>
      </v-sheet>
    </v-col>
  </v-row>
</template>

<script>
// eslint-disable-next-line camelcase
import Product_Add from '~/components/Product_Add.vue'
export default {
  name: 'StorePage',
  // eslint-disable-next-line camelcase
  components: { Product_Add },
  middleware: 'auth',
  data() {
    return {
      store_id: '',
      store: '',
      product: {},
      selectedItem: 0,
      items: [
        { text: 'Dashboard', icon: 'mdi-clock', to: '/store' },
        { text: 'เพิ่มสินค้า', icon: 'mdi-plus-box', to: '/store/add' },
        { text: 'Conversions', icon: 'mdi-flag' },
      ],
    }
  },
  async created() {
    await this.getRole()
    await this.getStore()
    await this.getProduct()
  },
  methods: {
    async getRole() {
      try {
        const response = await this.$axios.get(`/role`)
        response.data.forEach((val) => {
          if (val.store_id !== null) {
            this.store_id = val.store_id
          }
        })
        // eslint-disable-next-line no-console
        console.log(this.store_id)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.error(e)
      }
    },
    async getStore() {
      try {
        const response = await this.$axios.get(`/store/${this.store_id}`)
        response.data.data.forEach((val) => {
          this.store = val
        })

        // eslint-disable-next-line no-console
        console.log(this.store)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log('error' + e)
      }
    },
    async getProduct() {
      try {
        const response = await this.$axios.get(`/product/${this.store_id}`)
        this.product = response.data.data

        // eslint-disable-next-line no-console
        console.log(this.product)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log('error' + e)
      }
    },
  },
}
</script>