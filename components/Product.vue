<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <div>
    <v-card class="mx-auto rounded-md" min-width="250" color="" flat outlined>
      <div align="center" justify="center">
        <v-img v-if="!!img" height="250" contain :src="img"></v-img>
        <v-img
          v-else
          height="300"
          contain
          src="https://res.cloudinary.com/dqolakmsp/image/upload/v1674542994/samples/Img/No-Image-Placeholder.svg_wgjnzw.png"
        ></v-img>
      </div>
      <v-card-title class="">{{ title }}</v-card-title>
      <v-card-title class="mt-n4">{{
        Number(price.toFixed(1)).toLocaleString()
      }}</v-card-title>
      <v-card-text>
        <v-rating
          v-model="rating"
          background-color="orange lighten-3"
          color="orange"
          dense
          half-increments
          hover
          readonly
          size="18"
        ></v-rating>
        <span class="grey--text text--lighten-2 text-caption mr-2">
          ({{ rating }})
        </span>
      </v-card-text>
      <v-card-actions class="my-4">
        <v-btn
          class="mx-2 mt-n3"
          dark
          color="green"
          @click="
            checkFollow()
            checkCart()
            dialog = true
          "
        >
          เพิ่มเข้าตะกร้า
          <v-icon dark right> mdi-cart-variant </v-icon>
        </v-btn>
        <v-dialog
          v-model="dialog"
          transition="dialog-bottom-transition"
          max-width="600"
        >
          <v-card>
            <div v-if="$auth.loggedIn">
              <v-toolbar color="primary" dark
                >เพิ่มเข้าตะกร้า
                <v-spacer></v-spacer>
                <v-btn icon dark @click="dialog = false">
                  <v-icon>mdi-close</v-icon>
                </v-btn></v-toolbar
              >

              <v-card-text>
                <v-list-item>
                  <v-list-item-content>
                    <v-img v-if="!!img" height="250" contain :src="img"></v-img>
                    <v-list-item-title class="text-h5">{{
                      title
                    }}</v-list-item-title>
                    <v-list-item-subtitle class="text-body-1 my-3">
                      ราคา
                      {{ Number(price.toFixed(1)).toLocaleString() }}
                    </v-list-item-subtitle>
                    <v-list-item-subtitle class="text-body-1 my-3">
                      จำนวนสินค้าที่มี
                      {{ Number(number.toFixed(1)).toLocaleString() }}
                      ชิ้น
                    </v-list-item-subtitle>
                    <v-list-item-subtitle>
                      <v-btn
                        v-if="followProduct.length === 0"
                        class="mt-2"
                        dark
                        color="pink"
                        @click="followP"
                      >
                        ติดตามสินค้า
                        <v-icon dark right> mdi-cards-heart-outline </v-icon>
                      </v-btn>
                      <v-btn
                        v-else
                        class="mt-2"
                        dark
                        color="pink"
                        @click="unfollowP"
                      >
                        ติดตามสินค้าแล้ว
                        <v-icon dark right> mdi-cards-heart </v-icon>
                      </v-btn>
                    </v-list-item-subtitle>
                    <v-list-item-subtitle class="text-h6 mt-4">
                      <v-icon color="green" @click="decrement">
                        mdi-minus
                      </v-icon>

                      {{ bpm }}

                      <v-icon color="green" @click="increment">
                        mdi-plus
                      </v-icon>
                    </v-list-item-subtitle>
                  </v-list-item-content>
                </v-list-item>

                <v-card-actions>
                  <div v-if="CartProduct.length > 0 && bpm === 0">
                    <v-btn
                      class="mx-2 mt-n3"
                      dark
                      color="error"
                      @click="deleteCart"
                    >
                      ลบออกจากตะกร้า
                      <v-icon dark right> mdi-cart-remove </v-icon>
                    </v-btn>
                  </div>
                  <div v-else-if="CartProduct.length > 0 && bpm != 0">
                    <v-btn
                      class="mx-2 mt-n3"
                      dark
                      color="warning"
                      @click="updateCart"
                    >
                      แก้ไขตะกร้า
                      <v-icon dark right> mdi-cart-variant </v-icon>
                    </v-btn>
                    <v-btn
                      class="mx-2 mt-n3"
                      dark
                      color="error"
                      @click="deleteCart"
                    >
                      ลบออกจากตะกร้า
                      <v-icon dark right> mdi-cart-remove </v-icon>
                    </v-btn>
                  </div>
                  <div v-else>
                    <v-btn
                      class="mx-2 mt-n3"
                      dark
                      color="green"
                      @click="addCart"
                    >
                      เพิ่มเข้าตะกร้า
                      <v-icon dark right> mdi-cart-variant </v-icon>
                    </v-btn>
                  </div>
                </v-card-actions>
              </v-card-text>
            </div>
            <div v-else>
              <v-toolbar color="primary" dark>คำเตือน</v-toolbar>
              <v-card-text>
                <div justify="center">
                  <router-link to="/auth" class="text-decoration-none">
                    <v-btn>เข้าสู่ระบบ</v-btn>
                  </router-link>
                </div>
              </v-card-text>
              <v-card-actions class="justify-end">
                <v-btn text @click="dialog.value = false">Close</v-btn>
              </v-card-actions>
            </div>
          </v-card>
        </v-dialog>
      </v-card-actions>
    </v-card>
    <v-snackbar v-model="snackbar" :timeout="timeout">
      {{ text }}

      <template #action="{ attrs }">
        <v-btn color="blue" text v-bind="attrs" @click="snackbar = false">
          Close
        </v-btn>
      </template>
    </v-snackbar>
  </div>
</template>

<script>
export default {
  props: {
    title: {
      type: String,
      default: '',
    },
    price: {
      type: Number,
      default: 0,
    },
    number: {
      type: Number,
      default: 0,
    },
    date: {
      type: String,
      default: '',
    },
    id: {
      type: Number,
      default: 0,
    },
    img: {
      type: String,
      default: '',
    },
  },
  data: () => ({
    bpm: 1,
    rating: 4,
    followProduct: [],
    CartProduct: [],
    dialog: false,
    cart: {
      product_id: '',
      item_number: '',
    },
    snackbar: false,
    text: '',
    timeout: 2000,
  }),
  methods: {
    decrement() {
      if (this.bpm > 1 && this.CartProduct.length === 0) {
        this.bpm--
      }
      if (this.CartProduct.length > 0) {
        this.bpm--
      }
    },
    increment() {
      if (this.bpm < this.number) {
        this.bpm++
      }
    },
    async followP() {
      try {
        const response = await this.$axios.post(`/follow/product/${this.id}`)
        // eslint-disable-next-line no-console
        setTimeout(() => {
          this.checkFollow()
          this.snackbar = true
          this.text = 'ติดตามสินค้าแล้ว'
        }, response)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async unfollowP() {
      try {
        const response = await this.$axios.delete(`/follow/product/${this.id}`)
        // eslint-disable-next-line no-console
        setTimeout(() => {
          this.checkFollow()
          this.snackbar = true
          this.text = 'ยกเลิกติดตามสินค้าแล้ว'
        }, response)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async checkFollow() {
      try {
        const respo = await this.$axios.get(`/follow/product/${this.id}`)
        // eslint-disable-next-line no-console
        this.followProduct = respo.data
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async checkCart() {
      try {
        const respo = await this.$axios.get(`cart/${this.id}`)
        // eslint-disable-next-line no-console
        console.log(respo.data)
        this.CartProduct = respo.data
        if (respo.data.length > 0) {
          respo.data.forEach((val) => {
            this.bpm = val.item_number
          })
        }
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async addCart() {
      try {
        this.cart.product_id = this.id
        this.cart.item_number = this.bpm
        const respo = await this.$axios.post(`/cart`, this.cart)
        setTimeout(() => {
          this.checkCart()
          this.snackbar = true
          this.text = 'เพิ่มเข้าตะกร้าแล้ว'
        }, respo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async updateCart() {
      try {
        const respo = await this.$axios.patch(
          `/cart/${this.CartProduct[0].cart_id}`,
          {
            item_number: this.bpm,
          }
        )
        setTimeout(() => {
          this.checkCart()
          this.snackbar = true
          this.text = 'แก้ไขข้อมูลแล้ว'
        }, respo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async deleteCart() {
      try {
        const respo = await this.$axios.delete(
          `/cart/${this.CartProduct[0].cart_id}`
        )
        setTimeout(() => {
          this.checkCart()
          this.snackbar = true
          this.text = 'ลบข้อมูลแล้ว'
          this.bpm = 1
        }, respo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
  },
}
</script>

<style>
</style>