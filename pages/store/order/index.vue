<template>
  <div>
    <v-breadcrumbs :items="path" large></v-breadcrumbs>
    <v-card>
      <v-card-title>
        คำสั่งซื้อ
        <v-spacer></v-spacer>
        <v-btn v-show="order.length > 0 ? true : false" outlined color="primary" class="mx-2" @click="download">
          <v-icon>mdi-microsoft-excel</v-icon>
          ดาวน์โหลด
        </v-btn>
      </v-card-title>

      <v-data-table
        :headers="headers"
        :items="order"
        :search="search"
        :loading="loadingdata"
        no-data-text="ไม่มีข้อมูล"
        no-results-text="ไม่พบข้อมูล"
        class="elevation-1"
        loading-text="กำลังโหลดข้อมูล"
      >
        <template #top>
          <v-text-field
            v-model="search"
            append-icon="mdi-magnify"
            label="ค้นหาข้อมูล"
            class="mb-2"
            single-line
            hide-details
          ></v-text-field>
        </template>
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
            <p class="mb-0 mx-2">{{ item.item_number }}</p>
          </div>
        </template>
        <template #[`item.status`]="{ item }">
          <v-btn
            v-if="item.order_user_cancel === 1 || item.order_store_cancel === 1"
            outlined
            color="error"
          >
            ยกเลิกคำสั่งซื้อ
          </v-btn>
          <v-btn
            v-if="
              item.order_status === 0 &&
              item.order_user_cancel === 0 &&
              item.order_store_cancel === 0
            "
            outlined
            color="warning"
          >
            รอยืนยัน
          </v-btn>
          <v-btn
            v-else-if="
              item.order_status === 1 &&
              item.order_user_cancel === 0 &&
              item.order_store_cancel === 0
            "
            outlined
            color="info"
          >
            กำลังจัดส่งสินค้า
          </v-btn>
          <v-btn
            v-else-if="
              item.order_status === 2 &&
              item.order_user_cancel === 0 &&
              item.order_store_cancel === 0
            "
            outlined
            color="accent"
          >
            กำลังส่งสินค้า
          </v-btn>
          <v-btn
            v-else-if="
              item.order_status === 3 &&
              item.order_user_cancel === 0 &&
              item.order_store_cancel === 0
            "
            outlined
            color="primary"
          >
            คำสั่งสินค้าสำเร็จ
          </v-btn>
        </template>
        <template #[`item.created_at`]="{ item }">
          {{
            $moment(item.created_at)
              .add(543, 'year')
              .format('D MMMM YYYY HH:mm:ss')
          }}
        </template>
        <template #[`item.actions`]="{ item }">
          <router-link :to="`/store/order/${item.order_id}`">
            <v-btn color="primary"> รายละเอียด </v-btn>
          </router-link>
        </template>
      </v-data-table>
    </v-card>
  </div>
</template>
  
  <script>
export default {
  name: 'OrderIndex',
  layout: 'StoreLayout',
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
      order: [],
      store_id: '',
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
        {
          text: 'วันที่',
          value: 'created_at',
        },
        { text: 'สถานะ', value: 'status', sortable: false },
        { text: 'ตัวเลือก', value: 'actions', sortable: false },
      ],
      path: [
        {
          text: 'คำสั่งซื้อ',
          disabled: true,
          href: '/order',
        },
      ],
    }
  },
  async created() {
    await this.getRole()
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
    async getOrder() {
      this.loadingdata = true
      try {
        const respo = await this.$axios.get(`/order/store/${this.store_id}`)
        setTimeout(() => {
          this.order = respo.data
          this.loadingdata = false
        }, respo)
        // eslint-disable-next-line no-console
        console.log(this.order)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async download() {
      try {
        const respo = await this.$axios.get(`/report`)
        setTimeout(() => {
          const baseURL = respo.config.baseURL
          const links = respo.data.path
          location.href = baseURL
          // eslint-disable-next-line no-console
          const link = document.createElement('a')
          link.href = baseURL + '/download/' + links
          link.download = links
          link.click()
        }, respo)
        // eslint-disable-next-line no-console
        console.log(respo)
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