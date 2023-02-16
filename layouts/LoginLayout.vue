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
      <NavbarLogin :loginshow="false"/>
      <v-main>
        <v-container>
          <Nuxt />
        </v-container>
      </v-main>
    </v-app>
  </div>
</template>
  
  <script>
import NavbarLogin from '~/components/NavbarLogin.vue'
export default {
  name: 'LoginLayout',
  // eslint-disable-next-line vue/no-reserved-component-names
  components: { NavbarLogin },
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
    }, 500)
  },
}
</script>
  
  <style >
.marginLeft {
  margin-left: -90px;
}
.mtop {
  margin-top: 100px;
}
.mbottom {
  margin-bottom: 100px;
}
.v-card.borderme {
  border: 2px solid green !important;
}
.v-card.borderout {
  border: 1px solid #d5f0db !important;
}
.v-btn:not(.v-btn--round).v-size--default.add {
  min-width: 0px !important;
}
.theme--light.v-sheet--outlined.mobile {
  border: 2px solid black !important;
}
@media only screen and (max-width: 600px) {
  h2.title1 {
    font-size: 15px;
  }
  h2.title2 {
    font-size: 15px;
  }
  .top {
    margin-top: 20px;
  }
}
@media only screen and (min-width: 600px) {
  .top {
    margin-top: 70px;
  }
}
@media only screen and (min-width: 768px) {
  .top {
    margin-top: 120px;
  }
}
</style>