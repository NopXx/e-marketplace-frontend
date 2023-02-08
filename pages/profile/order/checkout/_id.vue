<template>
  <div>
    <!-- {{ $route.params.id }} -->
    <v-card>
      <v-card-text>
        <v-row>
          <v-col cols="12" md="4">
            <div class="text-h6 mb-3">ที่อยู่ในการจัดส่ง</div>
            <v-autocomplete
              v-model="userAddset"
              :items="userAdd"
              label="ที่อยู่"
              item-text="fullname"
              item-value="fullname"
              prepend-icon="mdi-map-marker-outline"
              return-object
            >
              <template #selection="data">
                <v-chip
                  v-show="address_id === data.item.user_a_id"
                  label
                  outlined
                  color="primary"
                  >ที่อยู่หลัก</v-chip
                >
                {{ `${data.item.fullname} - ${data.item.tel}` }}
              </template>
              <template #item="data">
                <v-list-item-content>
                  <v-list-item-title>
                    <v-chip
                      v-show="address_id === data.item.user_a_id"
                      label
                      outlined
                      color="info"
                      >ที่อยู่หลัก</v-chip
                    >
                    {{
                      `${data.item.fullname} - ${data.item.tel}`
                    }}</v-list-item-title
                  >
                </v-list-item-content>
                <!-- </template> -->
              </template>
            </v-autocomplete>
            <v-card-subtitle>
              {{ userAddset.fullname }}
              <br />
              {{ `${userAddset.address}` }}
              <br />
              {{ `ต. ${userAddset.sub_district} อ. ${userAddset.district}` }}
              <br />
              {{ `จ. ${userAddset.province} ${userAddset.zipcode}` }}
              <br />
              {{ userAddset.tel }}
            </v-card-subtitle>
          </v-col>
          <v-col cols="12" md="8">
            <v-card elevation="1">
              <v-card-text>
                <v-row>
                  <v-col cols="12" md="4">
                    <v-card-title>
                      {{ cartData.store_name }}
                    </v-card-title>
                    <v-img
                      :src="cartData.image"
                      max-height="250"
                      max-width="200"
                      dark
                    ></v-img>
                  </v-col>
                  <v-col cols="12" md="8">
                    <v-card-title> รายละเอียด </v-card-title>
                    <v-simple-table>
                      <template #default>
                        <thead>
                          <tr>
                            <th class="text-left">สินค้า</th>
                            <th class="text-left">ราคา</th>
                            <th class="text-left">จำนวนสินค้า</th>
                            <th class="text-left">ราคารวม</th>
                          </tr>
                        </thead>
                        <tbody>
                          <tr>
                            <td>
                              {{ cartData.product_name }}
                            </td>
                            <td>
                              {{
                                Number((
                                  cartData.product_price)?.toFixed(1)
                                ).toLocaleString()
                              }}
                            </td>
                            <td>{{ cartData.item_number }}</td>
                            <td>
                              {{
                                Number(
                                  (
                                    cartData.item_number *
                                    cartData.product_price
                                  )?.toFixed(1)
                                ).toLocaleString()
                              }}
                            </td>
                          </tr>
                        </tbody>
                      </template>
                    </v-simple-table>
                  </v-col>
                </v-row>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn outlined color="primary" @click="btnSave"> สั่งสินค้า </v-btn>
                </v-card-actions>
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
      </v-card-text>
    </v-card>
  </div>
</template>

<script>
export default {
  name: 'OrderCheck',
  layout: 'ProfileLayout',
  middleware: 'auth',
  data() {
    return {
      userAdd: [],
      userAddset: [],
      address_id: '',
      cartData: '',
    }
  },
  async created() {
    await this.getUserAdd()
    await this.getCart()
  },
  methods: {
    async getUserAdd() {
      try {
        const respo = await this.$axios.get(`/useradd`)
        this.userAdd = respo.data
        this.userAdd.forEach((val) => {
          if (val.address_default === 1) {
            this.userAddset = val
            this.address_id = val.user_a_id
          }
        })
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async getCart() {
      try {
        const respo = await this.$axios.get(`/cart`)
        respo.data.forEach((val) => {
          if (val.cart_id === parseInt(this.$route.params.id)) {
            // val
            // eslint-disable-next-line no-console
            this.cartData = val
          }
        })
        // eslint-disable-next-line no-console
        console.log(this.cartData)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async btnSave() {
        try {
            const respo = await this.$axios.post(`/order`, {
                cart_id: this.$route.params.id,
                user_address_id: this.userAddset.user_a_id
            })
            setTimeout(() => {
                // eslint-disable-next-line no-console
                console.log(respo)
            }, respo);
        } catch (e) {
            // eslint-disable-next-line no-console
            console.log(e)
        }
    }
  },
}
</script>