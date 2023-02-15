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
    <router-link to="/profile/like" class="text-decoration-none">
      <v-tooltip bottom>
        <template #activator="{ on, attrs }">
          <v-avatar
            size="40"
            v-bind="attrs"
            color="#ECF7EE"
            class="mr-2 mt-1"
            v-on="on"
          >
            <v-icon color="#FF6D59">mdi-cards-heart-outline</v-icon>
          </v-avatar>
        </template>
        <span>ติดตาม</span>
      </v-tooltip>
    </router-link>
    <!-- <v-badge color="#41AB55" overlap content="3" class="mr-5 mt-1">
      <v-avatar color="#ECF7EE" size="40">
        <v-icon color="#41AB55">mdi-basket-outline</v-icon>
      </v-avatar>
    </v-badge> -->
    <!-- cart -->
    <Cart />
    <!-- store -->
    <div v-show="userRole.store === 'store' ? true : false">
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

    <!-- admin -->
    <div v-show="userRole.admin === 'admin' ? true : false">
      <router-link to="/admin" class="text-decoration-none">
        <v-tooltip bottom>
          <template #activator="{ on, attrs }">
            <v-avatar
              size="40"
              color="#D5F0DB"
              class="mr-5 mt-1"
              v-bind="attrs"
              v-on="on"
            >
              <v-icon color="#0EA5E9">mdi-shield-crown-outline</v-icon>
            </v-avatar>
          </template>
          <span>ผู้ดูแลระบบ</span>
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
          <v-img
            v-if="userData.image === null"
            src="https://res.cloudinary.com/dqolakmsp/image/upload/v1676141396/image/profile/profile_sgnfpv.png"
            contain
          ></v-img>
          <v-img
            v-else
            :src="userData.image"
            aspect-ratio="1.4"
            max-height="125"
            max-width="110"
            contain
          ></v-img>
        </v-avatar>
      </v-chip>
    </router-link>
    <setting />
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
          <span class="primary--text d-none d-sm-flex text-body-1"
            >เข้าสู่ระบบ/สมัครบัญชี</span
          >
        </router-link>
      </v-chip>
    </div>
    <setting />
  </v-app-bar>
</template>
    
<script>
import Cart from './Cart.vue'
import Setting from './Setting.vue'
export default {
  name: 'NavBar',
  components: { Cart, Setting },
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
    userData: [],
    userRole: {
      store: '',
      admin: '',
    },
  }),
  async created() {
    await this.getRole()
    await this.getUser()
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
          if (val.role_name === 'store') {
            this.userRole.store = val.role_name
          } else if (val.role_name === 'admin') {
            this.userRole.admin = val.role_name
          }
        })
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async getUser() {
      this.overlay = true
      try {
        const response = await this.$axios.get('/user')
        this.userData = response.data[0]
        setTimeout(() => {
          this.overlay = false
        }, response)
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