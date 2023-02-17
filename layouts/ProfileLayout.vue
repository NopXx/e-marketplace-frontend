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
                  <v-list-item-title class="mb-2">ข้อมูลส่วนตัว</v-list-item-title>
                  <v-list-item-group v-model="selectedItem" color="primary">
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
      selectedItem: 0,
      items: [
        { text: 'ตะกร้า', icon: 'mdi-cart-variant', to: 'cart' },
        { text: 'คำสั่งซื้อ', icon: 'mdi-basket-outline', to: 'order' },
        { text: 'สินค้าที่ถูกใจ', icon: 'mdi-heart', to: 'like' },
        { text: 'ร้านค้า', icon: 'mdi-storefront-edit-outline', to: 'store' },
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