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
          <v-icon size="30" color="primary">mdi-palette</v-icon>
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
        <v-card-text>
          <v-card
            v-for="(theme, index) in themes"
            :key="index"
            class="my-2"
            :disabled="$vuetify.theme.themes.name === theme.name"
            hover
            outlined
            @click="setTheme(theme)"
          >
            <v-list-item>
              <v-list-item-content>
                <v-list-item-title class="font-weight-bold">
                  {{ theme.name }}</v-list-item-title
                >
              </v-list-item-content>
              <v-list-item-action>
                <v-avatar
                  v-if="$vuetify.theme.themes.name === theme.name"
                  color="primary"
                  size="30"
                >
                  <v-icon>mdi-check</v-icon>
                </v-avatar>
                <v-avatar v-else :color="theme.light.primary" size="30">
                </v-avatar>
              </v-list-item-action>
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
  name: 'ThemeChanger',
  data: () => ({
    menu: false,
    darkmode: false,
    localTheme: [],
    themes: [
      {
        name: 'Terra Cotta',
        dark: {
          primary: '#e56b62',
          secondary: '#8af2d9',
          accent: '#fcc2c3',
          info: '#446FCA',
          success: '#50D391',
          warning: '#F1B96F',
          error: '#D94126',
        },
        light: {
          primary: '#e56b62',
          secondary: '#8af2d9',
          accent: '#fcc2c3',
          info: '#446FCA',
          success: '#50D391',
          warning: '#F1B96F',
          error: '#D94126',
        },
      },
      {
        name: 'Blue Green',
        dark: {
          primary: '#4ac4c2',
          secondary: '#818edb',
          accent: '#01498c',
          neutral: '#2D2135',
          info: '#7F98F0',
          success: '#20A797',
          warning: '#DAAC16',
          error: '#ED4559',
        },
        light: {
          primary: '#4ac4c2',
          secondary: '#818edb',
          accent: '#01498c',
          neutral: '#2D2135',
          info: '#7F98F0',
          success: '#20A797',
          warning: '#DAAC16',
          error: '#ED4559',
        },
      },
      {
        name: 'Teal',
        dark: {
          primary: '#389499',
          secondary: '#a6ef99',
          accent: '#037067',
          info: '#42ADD7',
          success: '#1DE27C',
          warning: '#93610B',
          error: '#F42B15',
        },
        light: {
          primary: '#389499',
          secondary: '#a6ef99',
          accent: '#037067',
          info: '#42ADD7',
          success: '#1DE27C',
          warning: '#93610B',
          error: '#F42B15',
        },
      },
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
      {
        name: 'Pink',
        dark: {
          primary: '#ff79c6',
          secondary: '#bd93f9',
          accent: '#ffb86c',
          info: '#8be9fd',
          success: '#50fa7b',
          warning: '#f1fa8c',
          error: '#ff5555',
        },
        light: {
          primary: '#ff79c6',
          secondary: '#bd93f9',
          accent: '#ffb86c',
          info: '#8be9fd',
          success: '#50fa7b',
          warning: '#f1fa8c',
          error: '#ff5555',
        },
      },
      {
        name: 'info',
        dark: {
          primary: '#4b6bfb',
          secondary: '#7b92b2',
          accent: '#67cba0',
        },
        light: {
          primary: '#4b6bfb',
          secondary: '#7b92b2',
          accent: '#67cba0',
        },
      },
      {
        name: 'Blue',
        dark: {
          primary: '#570DF8',
          secondary: '#F000B8',
          accent: '#37CDBE',
          info: '#3ABFF8',
          success: '#36D399',
          warning: '#FBBD23',
          error: '#F87272',
        },
        light: {
          primary: '#570DF8',
          secondary: '#F000B8',
          accent: '#37CDBE',
          info: '#3ABFF8',
          success: '#36D399',
          warning: '#FBBD23',
          error: '#F87272',
        },
      },
      {
        name: 'Purple',
        dark: {
          primary: '#b96be0',
          secondary: '#4bdd4d',
          accent: '#edcb65',
          info: '#0ea5e9',
          success: '#78EDAB',
          warning: '#ECA03C',
          error: '#F54C2E',
        },
        light: {
          primary: '#b96be0',
          secondary: '#4bdd4d',
          accent: '#edcb65',
          info: '#0ea5e9',
          success: '#78EDAB',
          warning: '#ECA03C',
          error: '#F54C2E',
        }
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
  },
}
</script>
<style scoped></style>
