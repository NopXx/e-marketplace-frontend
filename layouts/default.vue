<template>
  <div>
    <div v-if="overlay">
      <v-app>
        <v-main>
          <v-container>
            <v-sheet class="pa-3">
              <v-skeleton-loader
                class="mx-auto my-auto"
                max-width="300"
                type="card"
              ></v-skeleton-loader>
            </v-sheet>
          </v-container>
        </v-main>
      </v-app>
    </div>
    <v-app v-else>
      <Bar />
      <!-- <AppBar /> -->
      <v-main>
        <v-container>
          <Nuxt />
        </v-container>
      </v-main>
    </v-app>
  </div>
</template>

<script>
// import AppBar from '../components/AppBar.vue'
import Bar from '../components/Bar.vue'
export default {
  name: 'DefaultLayout',
  components: { Bar },
  data () {
    return {
      overlay: false
    }
  },
  created () {
    this.overlay = true
    setTimeout(() => {
      this.overlay = false
    }, 1500)
  }
}
</script>