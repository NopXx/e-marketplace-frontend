<template>
    <div>
      <v-app-bar>
        <v-app-bar-nav-icon @click="drawer = true"></v-app-bar-nav-icon>
  
        <v-toolbar-title>ระบบยื่นคำร้อง</v-toolbar-title>
        <v-spacer />
        <ThemeChanger />
      </v-app-bar>
  
      <v-navigation-drawer v-model="drawer" absolute temporary>
        <v-list>
          <v-list-item class="px-2">
            <v-list-item-avatar color="primary">
              {{ $auth.loggedIn ? $auth.user.first_name.charAt(0) : 'STU' }}
            </v-list-item-avatar>
            <v-list-item-content>
              <v-list-item-title>{{
                $auth.loggedIn ? $auth.user.first_name : 'Student'
              }}</v-list-item-title>
              <v-list-item-subtitle>{{
              }}</v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item>
        </v-list>
        <v-divider></v-divider>
        
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
              <v-icon color="error">mdi-login</v-icon>
            </v-list-item-icon>
            <v-list-item-content>
              <v-list-item-title>เข้าสู่ระบบ</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
        </v-list>
        <template #append> </template>
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
      items: [
        {
          icon: 'mdi-folder-home',
          title: 'หน้าแรก',
          to: '/',
          usershow: true,
          logshow: false,
          adminshow: true
        },
        {
          icon: 'mdi-list-status',
          title: 'สถานะ',
          to: '/status',
          adminshow: false,
          logshow: true
        },
        {
          icon: 'mdi-playlist-plus',
          title: 'ลงทะเบียนคำขอ',
          to: '/register',
          usershow: true,
          adminshow: false,
          logshow: false
        },
        {
          icon: 'mdi-clipboard-list-outline',
          title: 'ตอบรับคำขอ',
          to: '/approve',
          usershow: false,
          logshow: true,
          adminshow: true
        },
        {
          icon: 'mdi-account-details-outline',
          title: 'ข้อมูลส่วนตัว',
          to: '/profile',
          usershow: true,
          adminshow: true,
          logshow: true
        }
      ]
    }),
    methods: {
      async Logout () {
        if (confirm('Logout....?')) {
          await this.$auth.logout()
          // localStorage.setItem('auth._token.local', '')
          this.$router.push({ path: '/auth/' })
        }
      }
    }
  }
  </script>
  
  <style></style>
  