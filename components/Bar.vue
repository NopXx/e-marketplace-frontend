<template>
  <div>
    <v-app-bar elevation="2">
      <v-app-bar-nav-icon @click="drawer = true"></v-app-bar-nav-icon>

      <v-toolbar-title
        ><router-link to="/" class="text-decoration-none"
          >E-Shop</router-link
        ></v-toolbar-title
      >
      <v-btn icon>
        <v-icon>mdi-magnify</v-icon>
      </v-btn>
      <v-spacer />
      <ThemeChanger />
    </v-app-bar>

    <v-navigation-drawer v-model="drawer" absolute temporary>
      <div v-if="$auth.loggedIn">
        <v-list>
          <v-list-item class="px-2">
            <v-list-item-avatar color="primary">
              {{ $auth.loggedIn ? $auth.user.f_name.charAt(0) : 'STU' }}
            </v-list-item-avatar>
            <v-list-item-content>
              <v-list-item-title class="px-2">{{
                $auth.loggedIn ? $auth.user.f_name : 'Student'
              }}</v-list-item-title>
              <v-divider class="my-1 py-1"></v-divider>

              <v-chip color="primary" filter label outlined>{{
                $auth.user.user_id
              }}</v-chip>
            </v-list-item-content>
          </v-list-item>
        </v-list>

        <v-divider></v-divider>
        <v-list>
          <v-list-item
            v-for="(item, i) in items"
            v-show="
              userRole.role_name === 'store' && item.store === false
                ? true
                : userRole.length === 0 && item.store === true
                ? true
                : item.usershow === true
                ? true
                : false
            "
            :key="i"
            :to="item.to"
            router
            exact
          >
            <v-list-item-icon>
              <v-icon color="primary">{{ item.icon }}</v-icon>
            </v-list-item-icon>
            <v-list-item-content>
              <v-list-item-title>{{ item.title }}</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
        </v-list>
      </div>
      <div v-else>
        <v-list>
          <v-list-item :to="items[0].to" router exact>
            <v-list-item-icon>
              <v-icon color="primary">{{ items[0].icon }}</v-icon>
            </v-list-item-icon>
            <v-list-item-content>
              <v-list-item-title>{{ items[0].title }}</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
        </v-list>
      </div>
      <template #append>
        <v-divider></v-divider>
        <v-list v-if="$auth.loggedIn">
          <v-list-item link @click="Logout()">
            <v-list-item-icon>
              <v-icon color="error">mdi-logout</v-icon>
            </v-list-item-icon>
            <v-list-item-content>
              <v-list-item-title>ออกจากระบบ</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
        </v-list>
        <v-list v-else>
          <v-list-item link to="/auth">
            <v-list-item-icon>
              <v-icon color="secondary">mdi-login</v-icon>
            </v-list-item-icon>
            <v-list-item-content>
              <v-list-item-title>เข้าสู่ระบบ</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
        </v-list>
      </template>
    </v-navigation-drawer>
  </div>
</template>
  
  <script>
import ThemeChanger from './ThemeChanger.vue'
export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: 'Bar',
  components: { ThemeChanger },
  data: () => ({
    drawer: false,
    group: null,
    userRole: [],
    items: [
      {
        icon: 'mdi-folder-home',
        title: 'หน้าแรก',
        to: '/',
        store: true,
        usershow: true,
        logshow: false,
        adminshow: true,
      },
      {
        icon: 'mdi-list-status',
        title: 'สถานะ',
        to: '/status',
        store: true,
        adminshow: false,
        usershow: true,
        logshow: true,
      },
      {
        icon: 'mdi-list-status',
        title: 'ร้านค้า',
        to: '/store',
        store: false,
        adminshow: false,
        // usershow: false,
        logshow: true,
      },
      {
        icon: 'mdi-account-details-outline',
        title: 'ข้อมูลส่วนตัว',
        to: '/profile',
        store: true,
        usershow: true,
        adminshow: true,
        logshow: true,
      },
    ],
  }),
  async created() {
    await this.getRole()
  },
  async mounted() {
    await this.getRole()
    // eslint-disable-next-line no-console
    console.log(this.userRole)
  },
  methods: {
    async Logout() {
      if (confirm('Logout....?')) {
        await this.$auth.logout()
        localStorage.setItem('auth._token.local', '')
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
  
  <style></style>
  