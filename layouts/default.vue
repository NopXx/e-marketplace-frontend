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
      <!-- <Bar /> -->
      <SideBar :drawer="drawer" />
      <RightSideBar />
      <!-- <AppBar /> -->
      <v-main>
        <v-container>
          <Header />
          <Nuxt />
        </v-container>
      </v-main>
    </v-app>
  </div>
</template>

<script>
import Header from '~/components/Header.vue'
// import AppBar from '../components/AppBar.vue'
import RightSideBar from '~/components/RightSideBar.vue'
import SideBar from '~/components/SideBar.vue'
// import Bar from '../components/Bar.vue'
export default {
  name: 'DefaultLayout',
  // eslint-disable-next-line vue/no-reserved-component-names
  components: { SideBar, RightSideBar, Header },
  data() {
    return {
      drawer: true,
      overlay: false,
    }
  },
  created() {
    this.overlay = true
    setTimeout(() => {
      this.overlay = false
    }, 1500)
  },
}
</script>