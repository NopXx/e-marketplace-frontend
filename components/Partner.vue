<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <v-row class="px-16 mt-10">
    <v-col v-for="(partner, i) in partners" :key="i" cols="3">
      <div align="center" justify="center">
        <v-img
          :src="require(`~/assets/img/${partner.img}`)"
          max-height="80"
          max-width="80"
          contain
        ></v-img>
      </div>
    </v-col>
  </v-row>
</template>

<script>
export default {
  data: () => ({
    partners: [
      { img: "airbnb.png" },
      { img: "adidas.png" },
      { img: "swords.png" },
      { img: "castle.png" },
    ],
  }),
};
</script>

<style>
</style>