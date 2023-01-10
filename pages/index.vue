<template>
  <v-row class="mt-n14">
    <v-col
      v-for="(item, i) in product.data"
      :key="i"
      cols="12"
      xs="12"
      sm="6"
      md="4"
      lg="3"
    >
      <card :id="item.product_id" :price="item.product_price" :title="item.product_name" />
    </v-col>
  </v-row>
  <!-- <v-row>
    <v-col v-for="item in product.data" :key="item.product_id">
      <card-product :price="item.product_price" :title="item.product_name" />
    </v-col>
    
  </v-row> -->
</template>

<script>
import Card from '~/components/Card.vue'
// import CardProduct from '~/components/CardProduct.vue'
export default {
  name: 'IndexPage',
  components: { Card },
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
