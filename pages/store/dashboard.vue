<template>
  <v-row>
    <v-col cols="2">
      <v-card elevation="2">
        <v-card-title> จำนวนสินค้าทั้งหมด </v-card-title>
        <v-card-text class="text-h5 text-center">
          {{ product.length }}
        </v-card-text>
      </v-card>
    </v-col>
    <v-col cols="2">
      <v-card elevation="2">
        <v-card-title> คำสั่งซื้อสินค้าทั้งหมด </v-card-title>
        <v-card-text class="text-h5 text-center">
          {{ order.length }}
        </v-card-text>
      </v-card>
    </v-col>
  </v-row>
</template>
  
  <script>
export default {
  name: 'DashboardPage',
  layout: 'StoreLayout',
  middleware: 'auth',
  data() {
    return {
      store_id: '',
      store: '',
      product: {},
      order: {},
    }
  },
  async created() {
    await this.getRole()
    await this.getStore()
    await this.getProduct()
    await this.getOrder()
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
        const response = await this.$axios.get(
          `/product/store/${this.store_id}`
        )
        this.product = response.data.data

        // eslint-disable-next-line no-console
        console.log(this.product)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log('error' + e)
      }
    },
    async getOrder() {
        try {
            const respo = await this.$axios.get(`/order/store/${this.store_id}`)
            this.order = respo.data
        } catch (e) {
            // eslint-disable-next-line no-console
            console.log(e)
        }
    }
  },
}
</script>