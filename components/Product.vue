<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <div>
    <v-card
      class="mx-auto rounded-md"
      min-width="300"
      color=""
      flat
      outlined
      @click="
        checkFollow()
        checkCart()
        getProductImage()
        dialog = true
      "
    >
      <div align="center" justify="center">
        <v-img v-if="!!img" height="250" contain :src="img">
          <template #placeholder>
            <v-row class="fill-height ma-0" align="center" justify="center">
              <v-progress-circular
                indeterminate
                color="grey lighten-5"
              ></v-progress-circular>
            </v-row>
          </template>
        </v-img>
        <v-img
          v-else
          height="300"
          contain
          src="https://res.cloudinary.com/dqolakmsp/image/upload/v1674542994/samples/Img/No-Image-Placeholder.svg_wgjnzw.png"
        >
          <template #placeholder>
            <v-row class="fill-height ma-0" align="center" justify="center">
              <v-progress-circular
                indeterminate
                color="grey lighten-5"
              ></v-progress-circular>
            </v-row>
          </template>
        </v-img>
      </div>
      <v-card-title class="text-truncate">{{ title }}</v-card-title>
      <v-card-title class="mt-n4"
        >{{ Number(price.toFixed(1)).toLocaleString() }} บาท</v-card-title
      >
      <v-card-text>
        ร้านค้า :
        <v-avatar v-if="store === null" color="primary" size="35" class="ml-2"
          ><v-img
            src="https://res.cloudinary.com/dqolakmsp/image/upload/v1676308580/assets/e-shop-no-image_uuubeq.png"
            aspect-ratio="1.4"
            max-height="125"
            max-width="110"
            contain
          ></v-img
        ></v-avatar>
        <v-avatar v-else size="45" class="ml-2">
          <v-img
            :src="store"
            aspect-ratio="1.4"
            max-height="125"
            max-width="110"
            contain
          ></v-img
        ></v-avatar>
        {{ storename }}
      </v-card-text>
      <v-card-actions class="my-4">
        <v-btn
          class="mx-2 mt-n3"
          dark
          color="primary"
          @click="
            checkFollow()
            checkCart()
            getProductImage()
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
            <div>
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
                    <v-carousel
                      v-if="!!img"
                      v-model="model"
                      height="300"
                      cycle
                      hide-delimiters
                      :show-arrows="image.length > 1 ? true : false"
                    >
                      <v-carousel-item
                        v-for="(item, i) in image"
                        :key="i"
                        reverse-transition="fade-transition"
                        transition="fade-transition"
                      >
                        <v-row
                          class="fill-height"
                          align="center"
                          justify="center"
                        >
                          <v-img height="250" contain :src="item.image">
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
                        </v-row>
                      </v-carousel-item>
                    </v-carousel>
                    <v-img
                      v-else
                      height="300"
                      contain
                      src="https://res.cloudinary.com/dqolakmsp/image/upload/v1674542994/samples/Img/No-Image-Placeholder.svg_wgjnzw.png"
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
                    <v-list-item-title class="text-h5">{{
                      title
                    }}</v-list-item-title>
                    <v-list-item-subtitle class="text-body-1 my-3">
                      ร้านค้า :
                      <v-avatar
                        v-if="store === null"
                        color="primary"
                        size="35"
                        class="ml-2"
                        ><v-img
                          src="https://res.cloudinary.com/dqolakmsp/image/upload/v1676308580/assets/e-shop-no-image_uuubeq.png"
                          aspect-ratio="1.4"
                          max-height="125"
                          max-width="110"
                          contain
                        ></v-img
                      ></v-avatar>
                      <v-avatar v-else size="45" class="ml-2">
                        <v-img
                          :src="store"
                          aspect-ratio="1.4"
                          max-height="125"
                          max-width="110"
                          contain
                        ></v-img
                      ></v-avatar>
                      {{ storename }}
                    </v-list-item-subtitle>
                    <v-list-item-subtitle class="text-body-1 my-3">
                      ราคา
                      {{ Number(price.toFixed(1)).toLocaleString() }}
                    </v-list-item-subtitle>
                    <v-list-item-subtitle class="text-body-1 my-3">
                      จำนวนสินค้าที่มี
                      {{ Number(number.toFixed(1)).toLocaleString() }}
                      ชิ้น
                    </v-list-item-subtitle>
                    <v-list-item-subtitle v-show="$auth.loggedIn">
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
                    <v-list-item-subtitle
                      v-show="$auth.loggedIn"
                      class="text-h6 mt-4"
                    >
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

                <v-card-actions v-if="$auth.loggedIn">
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
                <v-card-actions v-else>
                  <router-link to="/auth" class="text-decoration-none">
                    <v-btn color="green">เข้าสู่ระบบ</v-btn>
                  </router-link>
                  <v-spacer></v-spacer>
                  <v-btn outlined color="error" @click="dialog = false"
                    >ปิด</v-btn
                  >
                </v-card-actions>
              </v-card-text>
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
    store: {
      type: String,
      default: '',
    },
    storename: {
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
    image: [],
    model: 0,
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
    async getProductImage() {
      try {
        const respo = await this.$axios.get(`/image/product/${this.id}`)
        setTimeout(() => {
          this.image = respo.data
        }, respo)
      } catch (e) {}
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
.text-wrap {
  text-overflow: ellipsis; /* enables ellipsis */
  overflow: hidden; /* keeps the element from overflowing its parent */
}
</style>