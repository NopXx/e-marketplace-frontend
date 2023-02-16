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
                        v-if="store.image === null"
                        src="https://res.cloudinary.com/dqolakmsp/image/upload/v1676141396/image/profile/profile_sgnfpv.png"
                        contain
                      ></v-img>
                      <v-img
                        v-else
                        :src="store.image"
                        aspect-ratio="1.4"
                        max-height="125"
                        max-width="110"
                        contain
                      ></v-img>
                    </v-avatar>
                    <v-list-item-title class="text-h4">{{
                      store.store_name
                    }}</v-list-item-title>
                    <v-list-item-subtitle>{{
                      store.store_username
                    }}</v-list-item-subtitle>
                  </v-list-item-content>
                </v-list-item>
                <v-divider class="my-2"></v-divider>

                <v-list rounded>
                  <v-list-item-title>Admin Store</v-list-item-title>
                  <v-list-item-group v-model="selectedItem" color="primary">
                    <v-list-item
                      v-for="(item, i) in items"
                      :key="i"
                      :to="`/store/${item.to}`"
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
import Navbar from '~/components/Navbar.vue'
export default {
  name: 'StoreLayout',
  // eslint-disable-next-line vue/no-reserved-component-names
  components: { Navbar },
  data() {
    return {
      drawer: true,
      overlay: false,
      store_id: '',
      store: '',
      product: {},
      selectedItem: 0,
      items: [
        { text: 'Dashboard', icon: 'mdi-clock', to: 'dashboard' },
        { text: 'สินค้า', icon: 'mdi-basket-outline', to: 'list' },
        { text: 'เพิ่มสินค้า', icon: 'mdi-plus-box', to: 'add' },
        { text: 'คำสั่งซื้อ', icon: 'mdi-basket-outline', to: 'order' },
        { text: 'แก้ไข', icon: 'mdi-cog', to: 'edit' },
      ],
    }
  },
  async created() {
    this.overlay = true
    await this.getRole()
    await this.getStore()
    setTimeout(() => {
      this.overlay = false
    }, 500)
  },
  methods: {
    async getRole() {
      try {
        const response = await this.$axios.get(`/role`)
        response.data.forEach((val) => {
          if (val.store_id !== null) {
            this.store_id = val.store_id
          }
        })
        // eslint-disable-next-line no-console
        console.log(this.store_id)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.error(e)
      }
    },
    async getStore() {
      try {
        const response = await this.$axios.get(`/store/${this.store_id}`)
        response.data.forEach((val) => {
          this.store = val
        })

        // eslint-disable-next-line no-console
        console.log(this.store)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log('error' + e)
        this.$router.push('/')
      }
    },
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