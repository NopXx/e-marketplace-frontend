<template>
  <v-row>
    <v-col cols="12">
      <v-toolbar flat color="transparent">
        <v-toolbar-title class="text-h6">ประเภท</v-toolbar-title>
        <v-spacer></v-spacer>
        <v-btn-toggle v-model="toggle_exclusive" group color="#49D9A0">
          <v-btn>
            <v-icon>mdi-chevron-left</v-icon>
          </v-btn>
          <v-btn>
            <v-icon>mdi-chevron-right</v-icon>
          </v-btn>
        </v-btn-toggle>
      </v-toolbar>

      <Category />

      <v-toolbar flat color="transparent" class="mt-5">
        <v-toolbar-title class="text-h6">สินค้า</v-toolbar-title>
        <v-spacer></v-spacer>
        <v-btn rounded color="green" dark class="px-8">See All</v-btn>
      </v-toolbar>

      <v-row justify="center" class="space px-16">
        <v-col
          v-for="(item, i) in product.data"
          :key="i"
          cols="12"
          xs="12"
          sm="6"
          md="4"
        >
          <Product
            :id="item.product_id"
            :img="item.image"
            :price="item.product_price"
            :title="item.product_name"
          />
        </v-col>
      </v-row>
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
    }
  },
  created() {},
  mounted() {
    this.getProduct()
  },
  methods: {
    async getProduct() {
      try {
        const resp = await this.$axios.get('product')
        // eslint-disable-next-line no-console
        this.product = resp.data
      } catch (e) {
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
