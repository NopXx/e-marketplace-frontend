<template>
  <div>
    <v-breadcrumbs :items="path" large></v-breadcrumbs>
    <v-card>
      <v-card-title>
        <v-text-field
          v-model="search"
          append-icon="mdi-magnify"
          label="Search"
          single-line
          hide-details
        ></v-text-field>
      </v-card-title>
      <v-data-table
        :headers="headers"
        :items="cart"
        :search="search"
        :loading="loadingdata"
        group-by="store_name"
        loading-text="กำลังโหลดข้อมูล"
      >
        <!-- product -->
        <template #[`item.image`]="{ item }">
          <v-row justify="start" align="center">
            <v-col cols="12" md="3">
              <v-img
                v-if="!!item['image']"
                :src="item['image']"
                aspect-ratio="1.4"
                max-height="125"
                max-width="110"
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
                aspect-ratio="1"
                max-height="125"
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
            </v-col>
            <v-col cols="12" md="6">
              <p>{{ item.product_name }}</p>
            </v-col>
          </v-row>
        </template>
        <!-- price -->
        <template #[`item.product_price`]="{ item }">
          {{
            Number(
              (item['product_price'] * item['item_number']).toFixed(1)
            ).toLocaleString()
          }}
        </template>
        <!-- item_number -->
        <template #[`item.item_number`]="{ item }">
          <div class="d-flex align-center my-3">
            <v-btn
              outlined
              color="error"
              class="mr-2"
              @click="
                delItemNumber(
                  item.cart_id,
                  item.item_number,
                  item.product_number
                )
              "
              >-</v-btn
            >
            <p class="mb-0 mx-2">{{ item.item_number }}</p>
            <v-btn
              outlined
              color="primary"
              class="text-h5 ml-2"
              @click="
                addItemNumber(
                  item.cart_id,
                  item.item_number,
                  item.product_number
                )
              "
              >+</v-btn
            >
          </div>
          <div class="d-flex align-end">
            <p class="warning--text">
              เหลือสินค้าอยู่ {{ item.product_number }} ชิ้น
            </p>
          </div>
        </template>
        <!-- group -->
        <template #[`group.header`]="{ items }">
          <th colspan="5">
            <v-avatar size="35" color="#F8FAFC"
              ><v-icon color="warning">mdi-storefront-outline</v-icon></v-avatar
            >
            ร้านค้า :
            <v-avatar color="primary" size="35" class="ml-2">{{
              items[0].store_name[0]
            }}</v-avatar>
            {{ items[0].store_name }}
          </th>
        </template>
        <template #[`item.actions`]="{ item }">
          <router-link
            :to="`/profile/order/checkout/${item.cart_id}`"
            class="text-decoration-none"
          >
            <v-btn color="primary"> สั่งสินค้า </v-btn>
          </router-link>
        </template>
      </v-data-table>
    </v-card>
    <v-snackbar v-model="snackbar" :timeout="timeout">
      {{ text }}
      <template #action="{ attrs }">
        <v-btn color="blue" text v-bind="attrs" @click="snackbar = false">
          ปิด
        </v-btn>
      </template>
    </v-snackbar>
  </div>
</template>

<script>
export default {
  name: 'ProfileIndex',
  layout: 'ProfileLayout',
  middleware: 'auth',
  data() {
    return {
      loadingdata: false,
      snackbar: false,
      search: '',
      timeout: 1500,
      item_number: '',
      item_totle_number: '',
      text: '',
      cart: [],
      headers: [
        {
          text: 'สินค้า',
          value: 'image',
          align: 'start',
        },
        {
          text: 'ราคาสินค้า',
          value: 'product_price',
        },
        {
          text: 'จำนวนสินค้า',
          value: 'item_number',
        },
        { text: 'ตัวเลือก', value: 'actions', sortable: false },
      ],
      path: [
        {
          text: 'ตะกร้าสินค้า',
          disabled: false,
          href: '/profile',
        },
      ],
    }
  },
  async created() {
    await this.getCart()
  },
  methods: {
    async getCart() {
      this.loadingdata = true
      try {
        const respo = await this.$axios.get('/cart')
        setTimeout(() => {
          this.cart = respo.data
          this.loadingdata = false
        }, respo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async addItemNumber(id, number, totlenumber) {
      this.item_number = number
      this.item_totle_number = totlenumber
      if (this.item_number >= totlenumber) {
        return alert('ไม่สามารถเพิ่มจำนวนสินค้าชิ้นได้')
      }
      try {
        const itemnumber = this.item_number + 1
        const respo = await this.$axios.patch(`/cart/${id}`, {
          item_number: itemnumber,
        })
        setTimeout(() => {
          this.getCart()
          this.snackbar = true
          this.text = 'แก้ไขข้อมูลแล้ว'
        }, respo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async delItemNumber(id, number, totlenumber) {
      this.item_number = number
      this.item_totle_number = totlenumber
      if (this.item_number === 1) {
        if (confirm('ต้องการลบสินค้า ?')) {
          try {
            const respo = await this.$axios.delete(`/cart/${id}`)
            setTimeout(() => {
              this.getCart()
              this.snackbar = true
              this.text = 'ลบข้อมูลแล้ว'
            }, respo)
          } catch (e) {
            // eslint-disable-next-line no-console
            console.log(e)
          }
        } else {
          return
        }
      }
      try {
        const itemnumber = this.item_number - 1
        const respo = await this.$axios.patch(`/cart/${id}`, {
          item_number: itemnumber,
        })
        setTimeout(() => {
          this.getCart()
          this.snackbar = true
          this.text = 'แก้ไขข้อมูลแล้ว'
        }, respo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
  },
}
</script>

<style lang="scss">
tbody {
  tr {
    background-color: transparent !important;
  }
}
</style>