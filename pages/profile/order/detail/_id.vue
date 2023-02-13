<template>
  <div>
    <v-breadcrumbs :items="path" large></v-breadcrumbs>
    <v-card>
      <v-card-text>
        <v-row>
          <v-col cols="12" md="4">
            <div class="text-h6 mb-3">ที่อยู่ในการจัดส่ง</div>
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
            <v-card elevation="3">
              <v-card-text>
                <v-row>
                  <v-col cols="12" md="4">
                    <v-card-title>
                      <v-avatar
                        color="primary"
                        size="35"
                        class="mr-2"
                        >{{
                      }}</v-avatar>
                      {{ orderData.store_name }}
                    </v-card-title>
                    <v-img
                      :src="orderData.image"
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
                              v-show="
                                orderData.order_status !== 0 &&
                                orderData.order_tag !== null &&
                                orderData.transport_id !== null
                              "
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
                                คุณได้ยกเลิกคำสั่งซื้อแล้ว
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
                              orderData.order_user_cancel === 0 &&
                              orderData.order_store_cancel === 0
                            "
                            class="text-left"
                          >
                            <td colspan="2">
                              <v-btn outlined color="warning" class="ma-2">
                                รอผู้ขายยืนยัน
                              </v-btn>
                            </td>
                          </tr>
                          <tr
                            v-else-if="
                              orderData.order_status === 1 &&
                              orderData.order_user_cancel === 0 &&
                              orderData.order_store_cancel === 0
                            "
                          >
                            <td>
                              <v-btn outlined color="info" class="ma-2">
                                ผู้ขายกำลังจัดส่งสินค้า
                              </v-btn>
                            </td>
                            <td
                              v-show="
                                orderData.order_tag !== null &&
                                orderData.transport_id !== null
                              "
                            >
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
                              orderData.order_status === 2 &&
                              orderData.order_user_cancel === 0 &&
                              orderData.order_store_cancel === 0
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
                              orderData.order_user_cancel === 0 &&
                              orderData.order_store_cancel === 0
                            "
                          >
                            <td>
                              <v-btn outlined color="primary" class="ma-2">
                                สั่งสินค้าสำเร็จ
                              </v-btn>
                            </td>
                            <td>
                              <!-- <v-avatar size="45"> -->
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
                              <!-- </v-avatar> -->
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
                  <v-btn
                    v-if="
                      orderData.order_status === 0 &&
                      orderData.order_user_cancel === 0 &&
                      orderData.order_store_cancel === 0
                    "
                    outlined
                    color="error"
                    @click="dialog = !dialog"
                  >
                    ยกเลิกคำสั่งซื้อ
                  </v-btn>
                  <v-btn
                    v-else-if="
                      orderData.order_status === 1 &&
                      orderData.order_user_cancel === 0 &&
                      orderData.order_store_cancel === 0
                    "
                    outlined
                    color="warning"
                  >
                    ผู้ขายกำลังจัดส่งสินค้า
                  </v-btn>
                  <v-btn
                    v-else-if="
                      orderData.order_status === 2 &&
                      orderData.order_user_cancel === 0 &&
                      orderData.order_store_cancel === 0
                    "
                    outlined
                    color="info"
                    @click="btnConfirmOrder"
                  >
                    ได้รับสินค้าแล้ว
                  </v-btn>
                  <v-btn
                    v-else-if="
                      orderData.order_status === 3 &&
                      orderData.order_user_cancel === 0 &&
                      orderData.order_store_cancel === 0
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
            @click="OrderCancel"
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
  layout: 'ProfileLayout',
  middleware: 'auth',
  data() {
    return {
      snackbar: false,
      text: '',
      timeout: 2000,
      colors: '',
      userAddset: [],
      orderData: [],
      address_id: '',
      store_name: '',
      transport_name: '',
      items: [
        { value: 'สั่งซื้อสินค้าผิด' },
        { value: 'ไม่ต้องการซื้อ' },
        { value: 'มีสินค้าที่ดีกว่า' },
      ],
      cancelData: {
        cancel_order_detail: '',
      },
      dialog: false,
      path: [
        {
          text: 'คำสั่งซื้อ',
          disabled: false,
          href: '/profile/order',
        },
        {
          text: 'รายละเอียดคำสั่งซื้อ',
          disabled: true,
          href: '/profile/order',
        },
      ],
    }
  },
  async created() {
    await this.getUserAdd()
    await this.getOrder()
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
    async getOrder() {
      try {
        const respo = await this.$axios.get(`/order/${this.$route.params.id}`)
        respo.data.forEach((val) => {
          this.orderData = val
          this.store_name = val.store_name
          this.transport_name = val.transport_name
        })
        // eslint-disable-next-line no-console
        console.log(this.orderData)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async OrderCancel() {
      try {
        const respo = await this.$axios.patch(
          `/order/user/cancel-order/${this.$route.params.id}`,
          this.cancelData
        )
        setTimeout(() => {
          this.snackbar = true
          this.text = 'ยกเลิกคำสั่งซื้อแล้ว'
          this.colors = 'success'
          this.dialog = false
          this.getUserAdd()
          this.getOrder()
        }, respo)
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
          this.snackbar = true
          this.text = 'อัพเดทข้อมูลแล้ว'
          this.colors = 'success'
          this.getUserAdd()
          this.getOrder()
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