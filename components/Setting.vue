<template>
  <div>
    <v-menu
      v-model="menu"
      :close-on-content-click="false"
      :nudge-width="200"
      offset-y
    >
      <template #activator="{ on }">
        <v-btn large icon dark v-on="on">
          <v-icon size="30" color="primary">mdi-cog</v-icon>
        </v-btn>
      </template>
      <v-card>
        <v-list-item>
          <v-list-item-content
            ><v-list-item-title class="font-weight-bold">
              Dark Mode</v-list-item-title
            >
          </v-list-item-content>
          <v-list-item-action
            ><v-switch v-model="darkmode" inset />
          </v-list-item-action>
        </v-list-item>
        <v-divider />
        <v-card-text v-show="$auth.loggedIn">
          <v-card class="my-2" hover outlined @click="Logout()">
            <v-list-item>
              <v-list-item-icon>
                <v-icon>mdi-logout</v-icon>
              </v-list-item-icon>
              <v-list-item-content>
                <v-list-item-title class="font-weight-bold">
                  ออกจากระบบ
                </v-list-item-title>
              </v-list-item-content>
            </v-list-item>
          </v-card>
        </v-card-text>
        <v-divider />
        <v-card-actions>
          <v-btn text color="grey" @click="menu = false">Close</v-btn>
          <v-spacer />
        </v-card-actions>
      </v-card>
    </v-menu>
  </div>
</template>

<script>
export default {
  name: 'SettingComponent',
  data: () => ({
    menu: false,
    drawer: false,
    darkmode: false,
    localTheme: [],
    themes: [
      {
        name: 'Green',
        dark: {
          primary: '#18a05a',
          secondary: '#95f274',
          accent: '#67e5cc',
          info: '#2BA0E3',
          success: '#10704B',
          warning: '#EFA861',
          error: '#EB6B80',
        },
        light: {
          primary: '#18a05a',
          secondary: '#95f274',
          accent: '#67e5cc',
          info: '#2BA0E3',
          success: '#10704B',
          warning: '#EFA861',
          error: '#EB6B80',
        },
      },
    ],
  }),
  watch: {
    localTheme(oldval, newval) {
      this.setTheme(JSON.parse(this.localTheme))
    },
    darkmode(oldval, newval) {
      this.handledarkmode()
    },
  },
  created() {
    if (process.browser) {
      if (localStorage.getItem('theme')) {
        this.localTheme = localStorage.getItem('theme')
        // this.setTheme(JSON.parse(this.localTheme))
      } else {
        localStorage.setItem('theme', JSON.stringify(this.themes[0]))
        this.localTheme = localStorage.getItem('theme')
        // this.setTheme(JSON.parse(this.localTheme))
      }
      if (localStorage.getItem('DarkMode')) {
        const cookieValue = localStorage.getItem('DarkMode') === 'true'
        this.darkmode = cookieValue
      } else {
        this.handledarkmode()
      }
    }
  },
  methods: {
    setTheme(theme) {
      // close menu
      // eslint-disable-next-line no-console
      this.menu = false
      const name = theme.name
      const dark = theme.dark
      const light = theme.light
      // set themes
      Object.keys(dark).forEach((i) => {
        this.$vuetify.theme.themes.dark[i] = dark[i]
      })
      Object.keys(light).forEach((i) => {
        this.$vuetify.theme.themes.light[i] = light[i]
      })
      // also save theme name to disable selection
      this.$vuetify.theme.themes.name = name
      localStorage.setItem('theme', JSON.stringify(theme))
    },
    handledarkmode() {
      if (process.browser) {
        if (this.darkmode === true) {
          this.$vuetify.theme.dark = true
          localStorage.setItem('DarkMode', true)
        } else if (this.darkmode === false) {
          this.$vuetify.theme.dark = false
          localStorage.setItem('DarkMode', false)
        }
      }
    },
    async Logout() {
      if (confirm('Logout....?')) {
        await this.$auth.logout()
        // localStorage.setItem('auth._token.local', '')
        this.$router.push({ path: '/auth/' })
      }
    },
  },
}
</script>