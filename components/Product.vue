<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <v-card class="mx-auto rounded-xl" min-width="250" color="" flat outlined>
    <div align="center" justify="center">
      <v-img
        v-if="!!img"
        height="300"
        contain
        :src="img"
      ></v-img>
      <v-img
        v-else
        height="300"
        contain
        src="https://res.cloudinary.com/dqolakmsp/image/upload/v1674542994/samples/Img/No-Image-Placeholder.svg_wgjnzw.png"
      ></v-img>
    </div>

    <v-card-title class="">{{ title }}</v-card-title>
    <v-card-title class="mt-n4">{{ Number(price.toFixed(1)).toLocaleString() }}</v-card-title>

    <v-card-actions class="mx-2 mt-n4">
      <v-btn outlined class="mt-n2 add">
        <v-icon color="green" @click="decrement"> mdi-minus </v-icon>
      </v-btn>

      <strong class="mx-2" v-text="bpm"></strong>
      <v-btn outlined class="mt-n2 add">
        <v-icon color="green" @click="increment"> mdi-plus </v-icon>
      </v-btn>
      <v-spacer></v-spacer>
      <!-- <v-btn class="mx-2 mt-n3" fab dark small color="#FF6D59" @click="followP">
        <v-icon dark> mdi-cards-heart-outline </v-icon>
      </v-btn> -->
      <v-btn class="mx-2 mt-n3" fab dark small color="green">
        <v-icon dark> mdi-shopping </v-icon>
      </v-btn>
    </v-card-actions>
  </v-card>
</template>

<script>
export default {
  props: {
    title: {
      type: String,
      default: '',
    },
    price: {
      type: Number,
      default: 0,
    },
    date: {
      type: String,
      default: '',
    },
    id: {
      type: Number,
      default: 0,
    },
    img: {
      type: String,
      default: '',
    },
  },
  data: () => ({
    bpm: 1,
    products: [
      { img: 'pr1.png', title: 'Cabbage', subtitle: '1kg', price: '$13' },
      {
        img: 'pr2.png',
        title: "Perry's Ice Cream",
        subtitle: '1kg',
        price: '$23',
      },
      { img: 'pr3.png', title: 'Potato', subtitle: '1kg', price: '$17' },
      {
        img: 'pr4.png',
        title: 'Bundle Pack',
        subtitle: 'Potato, Papaya, Oil, Cabbage',
        price: '$40',
      },
      {
        img: 'pr5.png',
        title: 'Oreo Biscuit',
        subtitle: '270GM',
        price: '$20',
      },
      { img: 'pr6.png', title: 'Papaya', subtitle: '1kg', price: '$10' },
    ],
  }),
  methods: {
    decrement() {
      this.bpm--
    },
    increment() {
      this.bpm++
    },
    async followP() {
      try {
        const response = await this.$axios.post(`/follow/product/${this.id}`)
        // eslint-disable-next-line no-console
        console.log(response)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
  },
}
</script>

<style>
</style>