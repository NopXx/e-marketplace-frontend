<template>
  <v-col>
    <v-stepper v-model="e1">
      <v-stepper-header>
        <v-stepper-step :complete="e1 > 1" step="1">
          ชื่อสินค้า
        </v-stepper-step>

        <v-divider></v-divider>

        <v-stepper-step :complete="e1 > 2" step="2">
          รายละเอียด
        </v-stepper-step>

        <v-divider></v-divider>

        <v-stepper-step step="3"> Name of step 3 </v-stepper-step>
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
            </v-card>

            <v-btn color="primary" type="submit" :loading="loading">
              ต่อไป
            </v-btn>
          </form>
        </v-stepper-content>

        <v-stepper-content step="2">
          <v-card class="mb-12" height="auto">
            <form @submit.prevent="upload">
              <input type="file" @change="onFileChange" />
              <v-btn color="primary" :loading="loading1" type="submit">
                upload image
              </v-btn>
            </form>
            <form @submit.prevent="product_detail_add">
              <v-autocomplete
                v-model="product_detail.product_type_id"
                :items="product_type"
                label="ประเภทสินค้า"
                placeholder="เลือก..."
                item-text="product_type_name"
                item-value="product_type_id"
                :return-object="false"
                required
              ></v-autocomplete>
              <v-textarea
                v-model="product_detail.product_detail"
                :rules="nameRules"
                filled
                label="รายละเอียดสินค้า"
                required
              ></v-textarea>
              <v-btn color="primary" type="submit" :loading="loading2">
                บันทึก
              </v-btn>
            </form>
          </v-card>
        </v-stepper-content>

        <v-stepper-content step="3">
          <v-card class="mb-12" color="grey lighten-1" height="200px"></v-card>

          <v-btn color="primary" @click="e1 = 1"> Continue </v-btn>

          <v-btn text> Cancel </v-btn>
        </v-stepper-content>
      </v-stepper-items>
    </v-stepper>
  </v-col>
</template>

<script>
export default {
  name: 'ProductAddPage',
  data() {
    return {
      e1: 1,
      product: {
        product_name: '',
        product_price: '',
        product_number: '',
      },
      product_detail: {
        product_detail: '',
        product_type_id: '',
      },
      product_type: {},
      product_id: '',
      loading: false,
      loading1: false,
      loading2: false,
      nameRules: [(v) => !!v || 'กรุณาป้อนข้อมูล'],
      file: {},
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
        const respo = await this.$axios.post('product', {
          product_name: this.product.product_name,
          product_price: this.product.product_price,
          product_number: this.product.product_number,
        })

        setTimeout(() => {
          this.product_id = respo.data.product_id
          this.loading = false
          this.e1 = 2
        }, respo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
        this.loading = false
      }
    },
    async product_detail_add() {
      try {
        this.loading2 = true
        const resp = await this.$axios.post(
          `/product/detail/${this.product_id}`, this.product_detail)
        setTimeout(() => {
          this.loading2 = false
          this.e1 = 3
        }, resp)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
        this.loading2 = false
      }
    },
    onFileChange(e) {
      const selectedFile = e.target.files[0] // accessing file
      this.file = selectedFile
    //   console.log(this.file)
    },
    async upload() {
      try {
        const img = this.file
        const formData = new FormData();
        formData.append('image', img)
        formData.append('product_id', this.product_id)
        this.loading1 = true
        const response = await this.$axios.post('/upload/product', formData)
        setTimeout(() => {
          this.loading1 = false
        }, response)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
        this.loading1 = false
      }
    },
  },
}
</script>