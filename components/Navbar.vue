<template>
  <v-app-bar v-if="$auth.loggedIn" app flat>
    <router-link to="/" class="text-decoration-none">
      <v-badge color="#D5F0DB" dot>
        <h1 class="green--text">E-Shop</h1>
      </v-badge>
    </router-link>
    <settings-toggle />

    <v-spacer></v-spacer>

    <!-- <v-badge color="#FF6D59" overlap content="2" class="mr-2 mt-1">
      <v-avatar color="#FFF0EE" size="40">
        <v-icon color="#FF6D59">mdi-cards-heart-outline</v-icon>
      </v-avatar>
    </v-badge> -->
    <!-- like product -->
    <v-avatar size="40" color="#ECF7EE" class="mr-2 mt-1">
      <v-icon color="#FF6D59">mdi-cards-heart-outline</v-icon>
    </v-avatar>
    <!-- <v-badge color="#41AB55" overlap content="3" class="mr-5 mt-1">
      <v-avatar color="#ECF7EE" size="40">
        <v-icon color="#41AB55">mdi-basket-outline</v-icon>
      </v-avatar>
    </v-badge> -->
    <!-- cart -->
    <v-tooltip bottom>
      <template #activator="{ on, attrs }">
        <v-avatar
          size="40"
          color="#D5F0DB"
          class="mr-2 mt-1"
          v-bind="attrs"
          v-on="on"
        >
          <v-icon color="#41AB55">mdi-basket-outline</v-icon>
        </v-avatar>
      </template>
      <span>รถเข็น</span>
    </v-tooltip>
    <!-- store -->
    <div v-show="userRole.role_name === 'store' ? true : false">
      <router-link to="/store" class="text-decoration-none">
        <v-tooltip bottom>
          <template #activator="{ on, attrs }">
            <v-avatar
              size="40"
              color="#D5F0DB"
              class="mr-5 mt-1"
              v-bind="attrs"
              v-on="on"
            >
              <v-icon color="#0EA5E9">mdi-storefront-outline</v-icon>
            </v-avatar>
          </template>
          <span>ร้านค้า</span>
        </v-tooltip>
      </router-link>
    </div>

    <!-- <v-menu offset-y>
      <template slot="activator" slot-scope="{ on }"> -->
    <router-link to="/profile" class="text-decoration-none">
      <v-chip label class="ma-2 pa-6 mouse" v-on="on">
        <span class="dark--text d-none d-sm-flex">
          <strong> {{ $auth.user.f_name }}</strong>
        </span>

        <v-avatar size="30" class="ml-2">
          <v-img src="https://cdn.vuetifyjs.com/images/lists/2.jpg"></v-img>
        </v-avatar>
        <!-- <template slot="activator" slot-scope="{ on }">
          <v-btn icon dark v-on="on">
            <v-icon color="#878A94">mdi-chevron-down</v-icon>
          </v-btn>
        </template> -->
      </v-chip>
    </router-link>
    <!-- </template> -->
    <!-- <v-list>
        <v-list-item :to="items[0].to">
          <v-list-item-title>{{ items[0].title }}</v-list-item-title>
        </v-list-item>
        <v-list-item @click="Logout()">
          <v-list-item-title>{{ items[1].title }}</v-list-item-title>
        </v-list-item>
      </v-list>
    </v-menu> -->
    <ThemeChanger />
  </v-app-bar>
  <v-app-bar v-else app flat>
    <router-link to="/" class="text-decoration-none">
      <v-badge color="#D5F0DB" dot>
        <h1 class="green--text">E-Shop</h1>
      </v-badge>
    </router-link>

    <v-spacer></v-spacer>

    <div v-show="loginshow">
      <v-chip label class="ma-2">
        <router-link to="/auth" class="text-decoration-none">
          <span class="green--text d-none d-sm-flex text-body-1"
            >เข้าสู่ระบบ/สมัครบัญชี</span
          >
        </router-link>
      </v-chip>
    </div>
    <ThemeChanger />
  </v-app-bar>
</template>
    
<script>
import ThemeChanger from './ThemeChanger.vue'
export default {
  name: 'NavBar',
  components: { ThemeChanger },
  props: {
    loginshow: {
      type: Boolean,
      default: true,
    },
  },
  data: () => ({
    items: [
      { title: 'Profile', to: '/profile' },
      { title: 'Logout', to: '/logout' },
    ],
    userRole: [],
  }),
  async created() {
    await this.getRole()
  },
  methods: {
    async Logout() {
      if (confirm('Logout....?')) {
        await this.$auth.logout()
        // localStorage.setItem('auth._token.local', '')
        this.$router.push({ path: '/auth/' })
      }
    },
    async getRole() {
      try {
        const respo = await this.$axios.get(`role`)

        respo.data.forEach((val) => {
          if (val.store_id !== null) {
            this.userRole = val
          }
        })
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
  },
}
</script>
  
  <style>
.v-toolbar__title {
  font-size: 1rem !important;
}
.mouse {
  cursor: pointer;
}
</style>