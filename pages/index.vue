<template>
  <v-row>
    <v-col cols="12">
      <v-toolbar flat color="transparent">
        <v-toolbar-title class="text-h6">ประเภท</v-toolbar-title>
      </v-toolbar>

      <Category @selecttype="selectPtype" />
      <v-toolbar flat color="transparent" class="mt-5">
        <v-toolbar-title class="text-h6">สินค้า</v-toolbar-title>
        <v-spacer></v-spacer>
        <v-row justify="end">
          <v-col clos="3" sm="6" md="4">
            <v-text-field v-model="search" dense label="ค้นหาสินค้า">
            </v-text-field>
          </v-col>
          <v-col clos="3" sm="6" md="2">
            <v-btn color="primary" outlined @click="getSearch">ค้นหา</v-btn>
          </v-col>
        </v-row>
      </v-toolbar>
      <v-row v-if="loadshow" justify="center">
        <v-col cols="12" md="6">
          <v-skeleton-loader
            v-bind="attrs"
            type="article, actions"
          ></v-skeleton-loader>
        </v-col>
      </v-row>
      <div v-else>
        <v-row v-if="product.length > 0" justify="start" class="space px-16">
          <v-col
            v-for="(item, i) in product"
            :key="i"
            cols="12"
            xs="12"
            sm="6"
            md="3"
          >
            <Product
              :id="item.product_id"
              :img="item.image"
              :price="item.product_price"
              :title="item.product_name"
              :number="item.product_number"
            />
          </v-col>
        </v-row>
        <v-row v-else justify="center">
          <v-col cols="12"> ไม่พบข้อมูล </v-col>
        </v-row>
      </div>
    </v-col>
  </v-row>
</template>

<script>
import Category from '~/components/Category.vue'
import Product from '~/components/Product.vue'
export default {
  name: 'IndexPage',
  components: { Category, Product },
  // components: { CardProduct },
  data() {
    return {
      product: [],
      product_type: 0,
      loadshow: false,
      search: '',
    }
  },
  async created() {
    await this.getProduct()
  },
  methods: {
    async getProduct() {
      this.loadshow = true
      try {
        const resp = await this.$axios.get('product')
        // eslint-disable-next-line no-console
        setTimeout(() => {
          this.product = resp.data
          this.loadshow = false
        }, resp)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async getProductByType(id) {
      this.loadshow = true
      try {
        const resp = await this.$axios.get(`/product/search/type?id=${id}`)
        // eslint-disable-next-line no-console
        setTimeout(() => {
          this.product = resp.data
          this.loadshow = false
        }, resp)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async selectPtype(value) {
      this.product_type = value
      if (this.product_type === 0) {
        await this.getProduct()
        this.search = ''
      } else {
        await this.getProductByType(this.product_type)
        this.search = ''
      }
    },
    async getSearch() {
      this.loadshow = true
      try {
        const respo = await this.$axios.get(
          `/product/search?id=${this.product_type}&name=${this.search}`
        )
        setTimeout(() => {
          this.product = respo.data
          this.loadshow = false
        }, respo)
      } catch (e) {
        this.loadshow = true
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
  },
}
</script>

<style>
.pop {
  position: absolute;
  margin-left: auto;
  margin-right: auto;
  left: 0;
  right: 0;
  text-align: center;
}
</style>
