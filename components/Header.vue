<template>
  <div>
    <v-toolbar flat color="rgba(0,0,0,0)">
      <v-btn
        icon
        dark
        class="d-lg-none d-xl-flex"
        @click.stop="drawer = !drawer"
      >
        <v-icon>mdi-menu</v-icon>
      </v-btn>
      <v-spacer></v-spacer>
      <v-text-field
        label="Search on maufarm"
        class="pt-5 d-none d-sm-flex"
        dark
        filled
        prepend-inner-icon="mdi-magnify"
        solo
        flat
        background-color="transparent"
        rounded
        outlined
      ></v-text-field>
      <v-spacer></v-spacer>
      <v-btn dark class="mr-2">
        <v-icon left>mdi-filter-outline</v-icon>
        Filtre
      </v-btn>
      <v-btn dark class="mr-2">
        <v-icon left>mdi-upload</v-icon>
        Export
      </v-btn>
    </v-toolbar>
    <h1 class="white--text">SALES</h1>
    <p class="grey--text">
      A Great Way To Generate All The Motivation You Need To Get Fit
    </p>
    <v-app-bar dark color="rgba(0,0,0,0)" flat class="">
      <v-tabs color="#6F0DFF">
        <v-tabs-slider color="#6F0DFF"></v-tabs-slider>

        <v-tab class="withoutupercase">Fultillment Status View</v-tab>
        <v-tab class="withoutupercase">Real-time Trackning</v-tab>
        <v-tab class="withoutupercase">Sales View</v-tab>
        <v-tab class="withoutupercase">Balances</v-tab>
        <v-tab class="withoutupercase">Transactions</v-tab>
        <v-tab class="withoutupercase">Engagement Rate</v-tab>
      </v-tabs>
      <v-spacer></v-spacer>
      <v-btn dark text class="d-none d-sm-flex">
        <v-icon left>mdi-magnify</v-icon>
        SEARCH
      </v-btn>
      <v-btn rounded color="white" class="black--text">
        List
        <v-icon right>mdi-format-list-bulleted</v-icon>
      </v-btn>
      <v-btn icon>
        <v-icon>mdi-apps</v-icon>
      </v-btn>
    </v-app-bar>
    <v-divider color="grey" class="mb-2"></v-divider>
  </div>
</template>

<script>
export default {
  name: 'HeaderPage',
  data() {
    return {
      drawer: true,
    }
  },
}
</script>

<style>
</style>