<template>
  <v-row>
    <v-col cols="4">
      <v-card elevation="2" :loading="load_order">
        <v-card-title> จำนวนสินค้าทั้งหมด </v-card-title>
        <v-card-text class="text-h5 text-center">
          {{ product.length }}
        </v-card-text>
      </v-card>
    </v-col>
    <v-col cols="4">
      <v-card elevation="2" :loading="load_order">
        <v-card-title> คำสั่งซื้อสินค้าทั้งหมด {{ order.length }}</v-card-title>
        <v-card-text class="text-h5">
          <v-list v-show="!load_order">
            <!-- order ok -->
            <v-list-item>
              <v-list-item-icon>
                <v-icon color="green">mdi-check-circle-outline</v-icon>
              </v-list-item-icon>
              <v-list-item-content>
                <v-list-item-title>คำสั่งที่สำเร็จ</v-list-item-title>
                <v-list-item-subtitle>{{ order_ok }}</v-list-item-subtitle>
              </v-list-item-content>
            </v-list-item>
            <!-- order wait -->
            <v-list-item>
              <v-list-item-icon>
                <v-icon color="warning">mdi-clock-outline</v-icon>
              </v-list-item-icon>
              <v-list-item-content>
                <v-list-item-title>คำสั่งที่กำลังดำเนินการ</v-list-item-title>
                <v-list-item-subtitle>{{ order_wait }}</v-list-item-subtitle>
              </v-list-item-content>
            </v-list-item>
            <!-- order cancel -->
            <v-list-item>
              <v-list-item-icon>
                <v-icon color="error">mdi-close-circle-outline</v-icon>
              </v-list-item-icon>
              <v-list-item-content>
                <v-list-item-title>คำสั่งที่ยกเลิก</v-list-item-title>
                <v-list-item-subtitle>{{ order_cancel }}</v-list-item-subtitle>
              </v-list-item-content>
            </v-list-item>
          </v-list>
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
      order_ok: 0,
      order_wait: 0,
      order_cancel: 0,
      load_order: false,
    }
  },
  async created() {
    this.load_order = true
    await this.getRole()
    await this.getStore()
    await this.getProduct()
    await this.getOrder()
    this.load_order = false
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
        setTimeout(() => {
          respo.data.forEach((val) => {
            if (val.order_user_cancel === 1 || val.order_store_cancel === 1) {
              this.order_cancel += 1
            } else if (
              val.order_status !== 3 &&
              val.order_user_cancel === 0 &&
              val.order_store_cancel === 0
            ) {
              this.order_wait += 1
            } else if (
              val.order_status === 3 &&
              val.order_user_cancel === 0 &&
              val.order_store_cancel === 0
            ) {
              this.order_ok += 1
            }
          })
          this.order = respo.data
          
        }, respo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
  },
}
</script>