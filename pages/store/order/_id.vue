<template>
  <div>
    <v-breadcrumbs :items="path" large></v-breadcrumbs>
    <v-card>
      <v-card-text>
        <v-row>
          <v-col cols="12" md="2">
            <div class="text-h6 mb-3">ที่อยู่ในการจัดส่ง</div>
            <v-card-subtitle class="text-body-1">
              {{ orderData.fullname }}
              <br />
              {{ `${orderData.address}` }}
              <br />
              {{ `ต. ${orderData.sub_district} อ. ${orderData.district}` }}
              <br />
              {{ `จ. ${orderData.province} ${orderData.zipcode}` }}
              <br />
              {{ orderData.tel }}
            </v-card-subtitle>
          </v-col>
          <v-col cols="12" md="10">
            <v-card elevation="1">
              <v-card-text>
                <v-row>
                  <v-col cols="12" md="2">
                    <v-card-title> </v-card-title>
                    <v-img
                      :src="orderData.image"
                      max-height="250"
                      max-width="200"
                      dark
                    ></v-img>
                  </v-col>
                  <v-col cols="12" md="10">
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
                              {{ orderData.product_name }}
                            </td>
                            <td>
                              {{
                                Number(
                                  orderData.product_price?.toFixed(1)
                                ).toLocaleString()
                              }}
                            </td>
                            <td>{{ orderData.item_number }}</td>
                            <td>
                              {{
                                Number(
                                  (
                                    orderData.item_number *
                                    orderData.product_price
                                  )?.toFixed(1)
                                ).toLocaleString()
                              }}
                            </td>
                          </tr>
                        </tbody>
                      </template>
                    </v-simple-table>
                    <v-simple-table>
                      <template #default>
                        <thead>
                          <tr>
                            <th class="text-left">สถานะ</th>
                            <th
                              v-show="orderData.order_status === 1"
                              class="text-left"
                            >
                              เพิ่มหมายเลขพัสดุ
                            </th>
                            <th
                              v-show="orderData.order_status > 1"
                              class="text-left"
                            >
                              หมายเลขพัสดุ
                            </th>
                            <th
                              v-show="
                                orderData.order_user_cancel === 1 ||
                                orderData.order_store_cancel === 1
                              "
                              class="text-left"
                            >
                              เหตุผลที่ยกเลิก
                            </th>
                          </tr>
                        </thead>
                        <tbody>
                          <tr
                            v-if="
                              orderData.order_user_cancel === 1 &&
                              orderData.order_store_cancel === 0
                            "
                          >
                            <td>
                              <v-btn outlined color="error" class="ma-2">
                                ลูกค้าได้ยกเลิกคำสั่งซื้อแล้ว
                              </v-btn>
                            </td>
                            <td>
                              {{ orderData.order_cancel_detail }}
                            </td>
                          </tr>
                          <tr
                            v-if="
                              orderData.order_user_cancel === 0 &&
                              orderData.order_store_cancel === 1
                            "
                          >
                            <td>
                              <v-btn outlined color="error" class="ma-2">
                                ร้านค้าได้ยกเลิกคำสั่งซื้อแล้ว
                              </v-btn>
                            </td>
                            <td>
                              {{ orderData.order_cancel_detail }}
                            </td>
                          </tr>
                          <tr
                            v-if="
                              orderData.order_status === 0 &&
                              orderData.order_store_cancel === 0 &&
                              orderData.order_user_cancel === 0
                            "
                            class="text-left"
                          >
                            <td colspan="2">
                              <v-btn outlined color="warning" class="ma-2">
                                รอยืนยัน
                              </v-btn>
                            </td>
                          </tr>
                          <tr
                            v-else-if="
                              orderData.order_status === 1 &&
                              orderData.order_store_cancel === 0 &&
                              orderData.order_user_cancel === 0
                            "
                          >
                            <td>
                              <v-btn outlined color="info" class="ma-2">
                                กำลังจัดส่งสินค้า
                              </v-btn>
                            </td>
                            <td>
                              <form @submit.prevent="UpdateOrderTag">
                                <v-row justify="center" align="center">
                                  <v-col cols="12" md="4">
                                    <v-autocomplete
                                      v-model="OrderTag.transport_id"
                                      :items="transport"
                                      label="บริษัท"
                                      item-text="transport_name"
                                      item-value="transport_id"
                                      prepend-icon="mdi-truck-fast-outline"
                                      :return-object="false"
                                      required
                                    ></v-autocomplete>
                                  </v-col>
                                  <v-col cols="12" md="6">
                                    <v-text-field
                                      v-model="OrderTag.order_tag"
                                      label="หมายเลขพัสดุ"
                                      required
                                    ></v-text-field>
                                  </v-col>
                                  <v-col cols="12" md="2">
                                    <v-btn
                                      v-if="
                                        orderData.transport_id === null &&
                                        orderData.order_tag === null
                                      "
                                      outlined
                                      color="green"
                                      class="ma-2"
                                      type="submit"
                                    >
                                      บันทึก
                                    </v-btn>
                                    <v-btn
                                      v-else
                                      outlined
                                      color="warning"
                                      class="ma-2"
                                      type="submit"
                                    >
                                      แก้ไข
                                    </v-btn>
                                  </v-col>
                                </v-row>
                              </form>
                            </td>
                          </tr>
                          <tr
                            v-else-if="
                              orderData.order_status === 2 &&
                              orderData.order_store_cancel === 0 &&
                              orderData.order_user_cancel === 0
                            "
                          >
                            <td>
                              <v-btn outlined color="accent" class="ma-2">
                                กำลังส่งสินค้า
                              </v-btn>
                            </td>
                            <td colspan="2">
                              <v-img
                                v-if="orderData.transport_image === null"
                                src="https://res.cloudinary.com/dqolakmsp/image/upload/v1676308580/assets/e-shop-no-image_uuubeq.png"
                                contain
                              ></v-img>
                              <v-img
                                v-else
                                :src="orderData.transport_image"
                                aspect-ratio="1.6"
                                max-height="125"
                                max-width="100"
                                contain
                              ></v-img>
                              {{ orderData.transport_name }} :
                              {{ orderData.order_tag }}
                            </td>
                          </tr>
                          <tr
                            v-else-if="
                              orderData.order_status === 3 &&
                              orderData.order_store_cancel === 0 &&
                              orderData.order_user_cancel === 0
                            "
                          >
                            <td>
                              <v-btn outlined color="primary" class="ma-2">
                                สั่งสินค้าสำเร็จ
                              </v-btn>
                            </td>
                            <td colspan="2">
                              <v-img
                                v-if="orderData.transport_image === null"
                                src="https://res.cloudinary.com/dqolakmsp/image/upload/v1676308580/assets/e-shop-no-image_uuubeq.png"
                                contain
                              ></v-img>
                              <v-img
                                v-else
                                :src="orderData.transport_image"
                                aspect-ratio="1.6"
                                max-height="125"
                                max-width="100"
                                contain
                              ></v-img>
                              {{ orderData.transport_name }} :
                              {{ orderData.order_tag }}
                            </td>
                          </tr>
                        </tbody>
                      </template>
                    </v-simple-table>
                  </v-col>
                </v-row>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <div
                    v-if="
                      orderData.order_status === 0 &&
                      orderData.order_store_cancel === 0 &&
                      orderData.order_user_cancel === 0
                    "
                  >
                    <v-btn outlined color="primary" @click="btnConfirmOrder">
                      ยืนยันคำสั่งซื้อ
                    </v-btn>
                    <v-btn outlined color="error" @click="dialog = !dialog">
                      ยกเลิกคำสั่งซื้อ
                    </v-btn>
                  </div>

                  <v-btn
                    v-else-if="
                      orderData.order_status === 1 &&
                      orderData.order_store_cancel === 0 &&
                      orderData.order_user_cancel === 0
                    "
                    outlined
                    color="warning"
                    :disabled="
                      orderData.transport_id === null &&
                      orderData.order_tag === null
                        ? true
                        : false
                    "
                    @click="btnConfirmOrder"
                  >
                    ยืนยันการจัดส่งสินค้า
                  </v-btn>
                  <v-btn
                    v-else-if="
                      orderData.order_status === 2 &&
                      orderData.order_store_cancel === 0 &&
                      orderData.order_user_cancel === 0
                    "
                    outlined
                    color="info"
                  >
                    รอลูกค้ายืนยันรับสินค้า
                  </v-btn>
                  <v-btn
                    v-else-if="
                      orderData.order_status === 3 &&
                      orderData.order_store_cancel === 0 &&
                      orderData.order_user_cancel === 0
                    "
                    outlined
                    color="primary"
                  >
                    คำสั่งซื่อเสร็จสินแล้ว
                  </v-btn>
                </v-card-actions>
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
      </v-card-text>
    </v-card>
    <v-dialog v-model="dialog" persistent max-width="290">
      <v-card>
        <v-card-title class="text-h5">เหตุผลที่ยกเลิก</v-card-title>
        <v-card-text>
          <v-select
            v-model="cancelData.cancel_order_detail"
            :items="items"
            label="ตัวเลือก"
            item-text="value"
            item-value="value"
            :return-object="false"
          ></v-select>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="green darken-1"
            text
            @click="
              dialog = false
              cancelData.cancel_order_detail = ''
            "
          >
            ยกเลิก
          </v-btn>
          <v-btn
            color="error"
            :disabled="cancelData.cancel_order_detail === '' ? true : false"
            text
            @click="btnCancelOrder"
          >
            ตกลง
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
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
  name: 'OrderDetailIndex',
  layout: 'StoreLayout',
  middleware: 'auth',
  data() {
    return {
      snackbar: false,
      text: '',
      timeout: 2000,
      colors: '',
      dialog: false,
      userAddset: [],
      orderData: [],
      address_id: '',
      transport: [],
      OrderTag: {
        transport_id: '',
        order_tag: '',
      },
      cancelData: {
        cancel_order_detail: '',
      },
      items: [{ value: 'สินค้าหมด' }, { value: 'ไม่มีสินค้านี้แล้ว' }],
      path: [
        {
          text: 'คำสั่งซื้อ',
          disabled: false,
          href: '/store/order',
        },
        {
          text: 'รายละเอียดคำสั่งซื้อ',
          disabled: true,
          href: '/store/order',
        },
      ],
    }
  },
  async created() {
    await this.getOrder()
    await this.getTransport()
  },
  methods: {
    async getOrder() {
      try {
        const respo = await this.$axios.get(
          `/order/store/detail/${this.$route.params.id}`
        )
        respo.data.forEach((val) => {
          this.orderData = val
          this.OrderTag.transport_id = val.transport_id
          this.OrderTag.order_tag = val.order_tag
        })
        // eslint-disable-next-line no-console
        console.log(this.orderData)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async getTransport() {
      try {
        const respo = await this.$axios.get('/transport')
        this.transport = respo.data
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async btnConfirmOrder() {
      try {
        const respo = await this.$axios.patch(
          `/order/store/confirm-order/${this.$route.params.id}`
        )
        // eslint-disable-next-line no-console
        setTimeout(() => {
          this.getOrder()
          this.getTransport()
        }, respo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async btnCancelOrder() {
      try {
        const respo = await this.$axios.patch(
          `/order/store/cancel-order/${this.$route.params.id}`,
          this.cancelData
        )
        setTimeout(() => {
          this.snackbar = true
          this.text = 'ยกเลิกคำสั่งซื้อแล้ว'
          this.colors = 'success'
          this.dialog = false
          this.getOrder()
          this.getTransport()
        }, respo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async UpdateOrderTag() {
      try {
        const respo = await this.$axios.patch(
          `/order/store/order-tag/${this.$route.params.id}`,
          this.OrderTag
        )
        setTimeout(() => {
          this.getOrder()
          this.getTransport()
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