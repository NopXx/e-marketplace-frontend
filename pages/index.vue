<template>
  <v-row>
    <v-col v-for="item in product.data" :key="item.product_id">
      <card-product :price="item.product_price" :title="item.product_name" />
    </v-col>
    
  </v-row>
</template>

<script>
import CardProduct from '~/components/CardProduct.vue'
export default {
  name: 'IndexPage',
  components: { CardProduct },
  data() {
    return {
      product: []
    }
  },
   created() {
    
  },
  mounted() {
    this.getProduct()
  },
  methods: {
    async getProduct() {
      try {
        const resp = await this.$axios.get('product');
        // eslint-disable-next-line no-console
       this.product = resp.data
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e);
      }
    }
  }
}
</script>
