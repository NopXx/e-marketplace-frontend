<template>
  <div>
    <v-breadcrumbs :items="path" large></v-breadcrumbs>
    <v-card>
      <v-card-title>
        <v-text-field
          v-model="search"
          append-icon="mdi-magnify"
          label="ค้นหาข้อมูล"
          single-line
          hide-details
        ></v-text-field>
      </v-card-title>
      <v-data-table
        :headers="headers"
        :items="product"
        :search="search"
        no-data-text="ไม่มีข้อมูล"
        no-results-text="ไม่พบข้อมูล"
        class="elevation-1"
        loading-text="กำลังโหลดข้อมูล"
        :loading="loadingdata"
      >
        <template #[`item.image`]="{ item }">
          <v-img
            v-if="!!item['image']"
            :src="item['image']"
            aspect-ratio="1"
          ></v-img>
          <v-img
            v-else
            aspect-ratio="1"
            max-height="125"
            contain
            src="https://res.cloudinary.com/dqolakmsp/image/upload/v1674542994/samples/Img/No-Image-Placeholder.svg_wgjnzw.png"
          >
          </v-img>
        </template>
        <template #[`item.product_price`]="{ item }">
          {{ Number(item['product_price'].toFixed(1)).toLocaleString() }}
        </template>
        <template #[`item.actions`]="{ item }">
          <router-link
            :to="`/store/edit/${item['product_id']}`"
            class="text-decoration-none"
          >
            <v-tooltip bottom>
              <template #activator="{ on, attrs }">
                <v-btn
                  text
                  elevation="2"
                  class="ma-2 pa-6"
                  v-bind="attrs"
                  v-on="on"
                >
                  <v-icon class="m-2" color="green lighten-2"
                    >mdi-pencil-outline</v-icon
                  >
                </v-btn>
              </template>
              <span>แก้ไขสินค้า</span>
            </v-tooltip>
          </router-link>

          <!-- if product_show == 1 -->
          <v-tooltip v-if="item['product_show'] == 1" bottom>
            <template #activator="{ on, attrs }">
              <v-btn
                v-bind="attrs"
                text
                class="ma-2 pa-6"
                elevation="2"
                v-on="on"
                @click="delProduct(item['product_id'])"
              >
                <v-icon color="error">mdi-eye-off-outline</v-icon>
              </v-btn>
            </template>
            <span>ปิดแสดงสินค้า</span>
          </v-tooltip>
          <!-- else -->
          <v-tooltip v-else bottom>
            <template #activator="{ on, attrs }">
              <v-btn
                text
                class="ma-2 pa-6"
                elevation="2"
                v-bind="attrs"
                v-on="on"
                @click.stop="showCilck(item['product_id'])"
              >
                <v-icon color="success">mdi-eye-outline</v-icon>
              </v-btn>
            </template>
            <span>แสดงสินค้า</span>
          </v-tooltip>
        </template>
      </v-data-table>
    </v-card>
    <v-snackbar v-model="snackbar" :timeout="timeout">
      อัพเดทข้อมูลแล้ว
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
  name: 'ProductListPage',
  layout: 'StoreLayout',
  middleware: 'auth',
  data() {
    return {
      dialog: false,
      snackbar: false,
      timeout: 3000,
      search: '',
      loadingdata: false,
      headers: [
        {
          text: 'ไอดีสินค้า',
          align: 'center',
          value: 'product_id',
        },
        {
          text: 'ชื่อสินค้า',
          value: 'product_name',
        },
        {
          text: 'รูปสินค้า',
          value: 'image',
        },
        {
          text: 'ราคาสินค้า',
          value: 'product_price',
        },
        {
          text: 'จำนวนสินค้า',
          value: 'product_number',
        },
        { text: 'ตัวเลือก', value: 'actions', sortable: false },
      ],
      path: [
        {
          text: 'Store',
          disabled: false,
          href: '/store',
        },
        {
          text: 'จัดการสินค้า',
          disabled: true,
          href: '/store/list',
        },
      ],
      product_id: '',
      product: [],
      storeid: '',
    }
  },
  async created() {
    await this.getRole()
    await this.getProduct()
  },
  methods: {
    delCilck(id) {
      this.dialog = true
      this.product_id = id
    },
    async showCilck(id) {
      this.product_id = id
      try {
        // eslint-disable-next-line no-unused-vars
        const response = await this.$axios.patch(`/product/show/${id}`)
        setTimeout(() => {
          this.snackbar = true
          this.getProduct()
        }, response)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async delProduct(id) {
      // eslint-disable-next-line no-console
      console.log(id)
      this.dialog = false
      try {
        // eslint-disable-next-line no-unused-vars
        const response = await this.$axios.delete(`/product/show/${id}`)
        setTimeout(() => {
          this.getProduct()
        }, response)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async getRole() {
      try {
        const response = await this.$axios.get(`/role`)
        response.data.forEach((val) => {
          if (val.store_id !== null) {
            this.storeid = val.store_id
          }
        })
        // eslint-disable-next-line no-console
        console.log(this.store_id)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.error(e)
      }
    },
    async getProduct() {
      try {
        this.loadingdata = true
        const response = await this.$axios.get(`/product/store/${this.storeid}`)
        this.product = response.data.data

        setTimeout(() => {
          this.loadingdata = false
        }, response)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log('error' + e)
        this.loadingdata = false
      }
    },
  },
}
</script>