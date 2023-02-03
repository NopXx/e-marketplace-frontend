<template>
  <div>
    <v-col>
      <v-breadcrumbs :items="path" large></v-breadcrumbs>
      <v-stepper v-model="e1">
        <v-stepper-header>
          <v-stepper-step :complete="e1 > 1" step="1">
            ชื่อสินค้า
          </v-stepper-step>

          <v-divider></v-divider>

          <v-stepper-step :complete="e1 > 2" step="2">
            รายละเอียด
          </v-stepper-step>
        </v-stepper-header>

        <!-- item 1 -->
        <v-stepper-items>
          <v-stepper-content step="1">
            <form @submit.prevent="product_add">
              <v-card class="mb-12" height="auto">
                <v-text-field
                  v-model="product.product_name"
                  :rules="nameRules"
                  label="ชื่อสินค้า"
                  required
                ></v-text-field>

                <v-text-field
                  v-model="product.product_price"
                  :rules="nameRules"
                  label="ราคาสินค้า"
                  required
                ></v-text-field>

                <v-text-field
                  v-model="product.product_number"
                  :rules="nameRules"
                  label="จำนวนสินค้า"
                  required
                ></v-text-field>

                <v-autocomplete
                  v-model="product.product_type_id"
                  :items="product_type"
                  label="ประเภทสินค้า"
                  placeholder="เลือก..."
                  item-text="product_type_name"
                  item-value="product_type_id"
                  :return-object="false"
                  required
                ></v-autocomplete>
              </v-card>

              <v-btn color="primary" type="submit" :loading="loading">
                ต่อไป
              </v-btn>
            </form>
          </v-stepper-content>

          <v-stepper-content step="2">
            <v-card class="mb-12" height="auto">
              <v-row>
                <v-col cols="12" sm="12" md="6">
                  <v-file-input
                    v-model="file"
                    truncate-length="15"
                    label="อัพโหลดรูป"
                  ></v-file-input>
                </v-col>
                <v-col>
                  <v-btn
                    v-if="!!file"
                    color="primary"
                    :loading="loading1"
                    @click="upload"
                  >
                    อัพโหลด
                  </v-btn>
                  <v-btn v-else disabled color="primary" :loading="loading1">
                    อัพโหลด
                  </v-btn>
                </v-col>
              </v-row>

              <v-data-table
                :headers="headers"
                :items="product_image"
                :loading="loadingdata"
              >
                <!-- image -->
                <template #[`item.image`]="{ item }">
                  <div v-if="!!item['image']">
                    <v-img
                      :src="item['image']"
                      aspect-ratio="1"
                      max-height="125"
                      contain
                    ></v-img>
                  </div>
                </template>
                <!-- time -->
                <template #[`item.created_at`]="{ item }">
                  {{
                    $moment(item.created_at)
                      .add(543, 'year')
                      .format('D MMMM YYYY HH:mm:ss')
                  }}
                </template>
                <!-- show default image -->
                <template #[`item.default_image`]="{ item }">
                  <v-btn
                    v-if="item.default_image == 1"
                    class="mx-2"
                    fab
                    dark
                    small
                    color="green"
                  >
                    <v-icon dark> mdi-heart </v-icon>
                  </v-btn>
                  <v-btn v-else class="mx-2" fab dark small color="primary">
                    <v-icon dark> mdi-minus </v-icon>
                  </v-btn>
                </template>
                <!-- show action -->
                <!-- ----------------------- -->
                <template #[`item.actions`]="{ item }">
                  <template v-if="item.default_image != 1">
                    <v-tooltip bottom>
                      <template #activator="{ on, attrs }">
                        <v-btn
                          text
                          elevation="2"
                          v-bind="attrs"
                          :loading="loading2"
                          v-on="on"
                          @click="setDefault(item.img_id)"
                        >
                          <v-icon class="m-2" color="green">mdi-heart</v-icon>
                        </v-btn>
                      </template>
                      <span>รูปเริ่มต้น</span>
                    </v-tooltip>

                    <v-tooltip bottom>
                      <template #activator="{ on, attrs }">
                        <v-btn
                          v-bind="attrs"
                          text
                          elevation="2"
                          v-on="on"
                          @click="delCilck(item.img_id)"
                        >
                          <v-icon color="error">mdi-delete</v-icon>
                        </v-btn>
                      </template>
                      <span>ลบรูป</span>
                    </v-tooltip>
                  </template>

                  <!-- dialog -->
                  <v-dialog v-model="dialog" max-width="300">
                    <v-card>
                      <v-card-title class="text-h5"> คำเตือน </v-card-title>
                      <v-card-text class="text-body-1">
                        ต้องการลบรูปนี้ ? {{ img_id }}
                      </v-card-text>
                      <v-card-actions>
                        <v-spacer></v-spacer>
                        <v-btn
                          color="green darken-1"
                          text
                          @click="dialog = false"
                        >
                          ยกเลิก
                        </v-btn>
                        <v-btn
                          color="red darken-1"
                          text
                          @click="delImage(item.img_id)"
                        >
                          ตกลง
                        </v-btn>
                      </v-card-actions>
                    </v-card>
                  </v-dialog>
                  <!--  -->
                </template>
              </v-data-table>
              <form @submit.prevent="product_detail_add">
                <v-textarea
                  v-model="product_detail.product_detail"
                  :rules="nameRules"
                  filled
                  label="รายละเอียดสินค้า"
                  required
                ></v-textarea>
                <v-btn color="primary" type="submit" :disabled="product_image.length > 0 ? false : true" :loading="loading3">
                  บันทึก
                </v-btn>
              </form>
            </v-card>
          </v-stepper-content>
        </v-stepper-items>
      </v-stepper>
    </v-col>
    <v-snackbar v-model="snackbar" :timeout="timeout">
      {{ text }}
      <template #action="{ attrs }">
        <v-btn color="primary" text v-bind="attrs" @click="snackbar = false">
          ปิด
        </v-btn>
      </template>
    </v-snackbar>
  </div>
</template>

<script>
export default {
  name: 'ProductAddPage',
  layout: 'StoreLayout',
  middleware: 'auth',
  data() {
    return {
      e1: 1,
      product_image: [],
      product: {
        product_name: '',
        product_price: '',
        product_number: '',
        product_type_id: '',
      },
      product_detail: {
        product_detail: '',
      },
      path: [
        {
          text: 'Store',
          disabled: false,
          href: '/store',
        },
        {
          text: 'เพิ่มสินค้า',
          disabled: true,
          href: '/store/add',
        },
      ],
      headers: [
        {
          text: 'รูปสินค้า',
          value: 'image',
        },
        {
          text: 'เวลาอัพโหลด',
          value: 'created_at',
        },
        {
          text: 'รูปเริ่มต้น',
          value: 'default_image',
        },
        { text: 'ตัวเลือก', value: 'actions', sortable: false },
      ],
      product_type: {},
      product_id: '',
      loadingdata: false,
      loading: false,
      loading1: false,
      loading2: false,
      loading3: false,
      nameRules: [(v) => !!v || 'กรุณาป้อนข้อมูล'],
      file: '',
      timeout: 3000,
      snackbar: false,
      dialog: false,
      img_id: '',
      text: '',
    }
  },
  async created() {
    await this.producttype()
  },
  methods: {
    async producttype() {
      try {
        const response = await this.$axios.get('/product-type')
        this.product_type = response.data.data
        // eslint-disable-next-line no-console
        console.log(this.product_type)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async product_add() {
      try {
        this.loading = true
        const respo = await this.$axios.post('product', this.product)

        setTimeout(() => {
          this.product_id = respo.data.product_id
          this.loading = false
          this.e1 = 2
          this.getProductImage()
        }, respo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
        this.loading = false
      }
    },
    async product_detail_add() {
      try {
        this.loading3 = true
        const resp = await this.$axios.post(
          `/product/detail/${this.product_id}`,
          this.product_detail
        )
        setTimeout(() => {
          this.loading3 = false
          this.e1 = 3
          this.$router.push('/store/list')
        }, resp)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
        this.loading3 = false
      }
    },
    async getProductImage() {
      this.loadingdata = true
      try {
        const respo = await this.$axios.get(`/image/product/${this.product_id}`)
        setTimeout(() => {
          this.product_image = respo.data
          this.loadingdata = false
        }, respo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async upload() {
      try {
        const img = this.file
        const formData = new FormData()
        formData.append('image', img)
        formData.append('product_id', this.product_id)
        this.loading1 = true
        const response = await this.$axios.post('/upload/product', formData)
        setTimeout(() => {
          this.loading1 = false
          this.getProductImage()
          this.file = ''
        }, response)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
        this.loading1 = false
      }
    },
    async setDefault(imgid) {
      this.loading2 = true
      try {
        const response = await this.$axios.patch(
          `/image/product/${this.product_id}`,
          {
            img_id: imgid,
          }
        )
        setTimeout(() => {
          this.getProductImage()
          this.text = 'อัพเดทข้อมูลแล้ว'
          this.snackbar = true
          this.loading2 = false
        }, response)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
        this.loading2 = false
      }
    },
    delCilck(imgid) {
      this.dialog = true
      this.img_id = imgid
    },
    async delImage(imgid) {
      try {
        const respo = await this.$axios.delete(`/image/delete`, {
          data: { id: imgid },
        })
        this.dialog = false
        setTimeout(() => {
          this.getProductImage()
          this.text = 'ลบข้อมูลแล้ว'
          this.snackbar = true
        }, respo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
  },
}
</script>