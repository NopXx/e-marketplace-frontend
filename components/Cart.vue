<!-- eslint-disable vue/no-template-shadow -->
<template>
  <div class="text-center">
    <v-menu
      v-model="menu"
      :close-on-content-click="false"
      :nudge-width="200"
      :open-on-hover="false"
      bottom
      close-delay="100"
      content-class="rounded"
      left
      max-height="500"
      offset-y
      open-delay="60"
    >
      <template #activator="{ on: menu, attrs }">
        <v-tooltip bottom>
          <template #activator="{ on: tooltip }">
            <!-- <v-badge
              v-if="cart.length > 0"
              color="#FF6D59"
              overlap
              content="2"
              class="mr-2 mt-1"
            >
              <v-avatar
                size="40"
                color="#ECF7EE"
                class="mr-2 mt-1"
                v-bind="attrs"
                v-on="{ ...tooltip, ...menu }"
              >
                <v-icon color="#41AB55">mdi-cart-variant</v-icon>
              </v-avatar>
            </v-badge> -->
            <v-badge
              color="#FF6D59"
              overlap
              :content="cart_number.length"
              class="mr-2 mt-1"
            >
            <v-avatar
              size="40"
              color="#ECF7EE"
              class="mr-2 mt-1"
              v-bind="attrs"
              v-on="{ ...tooltip, ...menu }"
              @click="getCart(); getCartNumber();"
            >
              <v-icon color="#41AB55">mdi-cart-variant</v-icon>
            </v-avatar>
            </v-badge>
          </template>
          <span>ตะกร้าสินค้า</span>
        </v-tooltip>
      </template>
      <v-card>
        <v-card-title> ตะกร้าสินค้า </v-card-title>
        <v-card-text v-if="cart.length > 0">
          <v-list-item v-for="(item, i) in cart" :key="i">
            <v-list-item-avatar>
              <v-img :src="item.image"></v-img>
            </v-list-item-avatar>

            <v-list-item-content>
              <v-list-item-title>{{ item.product_name }}</v-list-item-title>
              <v-list-item-subtitle>
                ราคา
                {{
                  Number(
                    (item.product_price * item.item_number).toFixed(1)
                  ).toLocaleString()
                }}
              </v-list-item-subtitle>
              <v-list-item-subtitle>
                จำนวน {{ item.item_number }}
              </v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item>
          <v-card-actions>
            <v-list rounded>
              <v-list-item to="profile">
                <v-list-item-group color="success">
                  <v-list-item-content>
                    <v-list-item-title>แสดงทั้งหหมด</v-list-item-title>
                  </v-list-item-content>
                </v-list-item-group>
              </v-list-item>
            </v-list>
          </v-card-actions>
        </v-card-text>
        <v-card-text v-else>
          <div>ไม่ข้อมูล</div>
        </v-card-text>
      </v-card>
    </v-menu>
  </div>
</template>

<script>
export default {
  name: 'CartComponent',
  data() {
    return {
      menu: false,
      cart: [],
      cart_number: '',
    }
  },
  async created() {
    await this.getCart()
    await this.getCartNumber()
  },
  beforeMount() {
    this.getCart()
    this.getCartNumber()
  },
  methods: {
    async getCart() {
      try {
        const respo = await this.$axios.get('/cart?limit=5')
        // eslint-disable-next-line no-console
        this.cart = respo.data
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async getCartNumber() {
      try {
        const respo = await this.$axios.get('/cart')
        // eslint-disable-next-line no-console
        this.cart_number = respo.data
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
  },
}
</script>