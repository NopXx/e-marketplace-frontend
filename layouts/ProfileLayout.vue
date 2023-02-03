<template>
  <div>
    <v-app v-if="overlay">
      <v-main>
        <v-container>
          <v-skeleton-loader
            v-bind="attrs"
            type="table-heading, list-item-two-line, image, table-tfoot"
          ></v-skeleton-loader>
        </v-container>
      </v-main>
    </v-app>
    <v-app v-else>
      <Navbar />
      <v-main>
        <v-container>
          <v-row>
            <v-col cols="2">
              <v-sheet rounded="lg" outlined>
                <v-list-item class="text-center">
                  <v-list-item-content>
                    <v-avatar size="128">
                      <img
                        src="https://cdn.vuetifyjs.com/images/john.jpg"
                        alt="John"
                      />
                    </v-avatar>
                    <v-list-item-title class="text-h5">
                      {{ userData.f_name + ' - ' + userData.l_name }}
                    </v-list-item-title>
                    <v-list-item-subtitle>
                      {{ userData.username }}
                    </v-list-item-subtitle>
                  </v-list-item-content>
                </v-list-item>
                <v-divider class="my-2"></v-divider>

                <v-list rounded>
                  <v-list-item-group color="success">
                    <v-list-item
                      v-for="(item, i) in items"
                      :key="i"
                      :to="`/profile/${item.to}`"
                    >
                      <v-list-item-icon>
                        <v-icon>{{ item.icon }}</v-icon>
                      </v-list-item-icon>
                      <v-list-item-content>
                        <v-list-item-title>{{ item.text }}</v-list-item-title>
                      </v-list-item-content>
                    </v-list-item>
                  </v-list-item-group>
                </v-list>
              </v-sheet>
            </v-col>
            <v-col>
              <Nuxt />
            </v-col>
          </v-row>
        </v-container>
      </v-main>
    </v-app>
  </div>
</template>

<script>
export default {
  name: 'ProfileLayout',
  data() {
    return {
      overlay: false,
      userData: [],
      items: [
        { text: 'คำสั่งซื้อ', icon: 'mdi-basket-outline', to: '' },
        { text: 'สินค้าที่ถูกใจ', icon: 'mdi-heart', to: 'like' },
        { text: 'แก้ไข', icon: 'mdi-cog', to: 'edit' },
      ],
    }
  },
  async created() {
    await this.getUser()
  },
  methods: {
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