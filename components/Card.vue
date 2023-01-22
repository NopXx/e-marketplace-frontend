<template>
  <v-card class="mx-auto my-12 rounded-xl" max-width="374" color="#151515">
    <div v-if="!!img">
      <v-img height="170" :src="img">
        <template #placeholder>
          <v-row class="fill-height ma-0" align="center" justify="center">
            <v-progress-circular
              indeterminate
              color="grey lighten-5"
            ></v-progress-circular>
          </v-row>
        </template>
      </v-img>
    </div>
    <div v-else>
      <v-img
        height="170"
        src="https://cdn.vuetifyjs.com/images/cards/sunshine.jpg"
      ></v-img>
    </div>
    <v-toolbar color="transparent" class="mt-n7" flat>
      <v-avatar color="black" rounded class="mr-2" dark>
        <div class="three">
          <div class="four">
            <span class="white--text caption">{{ price }}</span>
          </div>
          <div class="five">
            <span class="green--text caption">HOLD</span>
          </div>
        </div>
      </v-avatar>
    </v-toolbar>
    <v-card-title class="grey--text caption">{{ id }}</v-card-title>
    <v-card-title class="grey--text text-grey-darken-1 caption mt-n6">{{
      date
    }}</v-card-title>
    <v-card-text class="white--text text-grey-darken-1 mt-n2">{{
      title
    }}</v-card-text>

    <!-- <v-card-text class="mt-n4">
      <v-chip-group active-class="deep-purple accent-4 white--text" column>
        <v-chip label dark>{{ shoe.pay1 }}</v-chip>
        <v-chip label dark>{{ shoe.pay2 }}</v-chip>
        <v-spacer></v-spacer>
        <v-avatar size="40">
          <v-img :src="shoe.avatar"></v-img>
        </v-avatar>
      </v-chip-group>
    </v-card-text> -->
  </v-card>
</template>

<script>
export default {
  name: 'CardShop',
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
      type: String,
      default: '',
    },
    img: {
      type: String,
      default: '',
    },
  },
}
</script>

<style>
.three {
  width: 50px;
  height: 50px;
}
.four {
  width: 50px;
  height: 25px;
  background: black;
}
.five {
  width: 50px;
  height: 25px;
  background: #042a0f;
}
</style>