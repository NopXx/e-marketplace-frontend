<template>
  <div>
    <v-card-title>
      <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        label="ค้นหาข้อมูล"
        single-line
        hide-details
      ></v-text-field>
    </v-card-title>
    <v-data-table
      :headers="headers"
      :items="userfollow"
      :search="search"
      no-data-text="ไม่มีข้อมูล"
      class="elevation-1"
      :loading="loadingdata"
    >
      <template #[`item.product_price`]="{ item }">
        <v-row>
          <v-col>{{
            Number(item.product_price.toFixed(1)).toLocaleString()
          }}</v-col>
        </v-row>
        <v-row>
          <v-col>
            <p class="warning--text">
              เหลือสินค้าอยู่ {{ item.product_number }} ชิ้น
            </p>
          </v-col>
        </v-row>
      </template>
      <!-- image -->
      <template #[`item.image`]="{ item }">
        <div v-if="!!item['image']">
          <v-img
            :src="item['image']"
            aspect-ratio="1"
            max-height="125"
            contain
          ></v-img>
        </div>
      </template>
      <!-- show default image -->
      <template #[`item.default_image`]="{ item }">
        <v-btn
          v-if="item.default_image == 1"
          class="mx-2"
          fab
          dark
          small
          color="green"
        >
          <v-icon dark> mdi-heart </v-icon>
        </v-btn>
        <v-btn v-else class="mx-2" fab dark small color="primary">
          <v-icon dark> mdi-minus </v-icon>
        </v-btn>
      </template>
      <!-- show action -->
      <!-- ----------------------- -->
      <template #[`item.actions`]="{ item }">
        <v-row>
          <v-col v-show="item.product_number > 0" cols="12" md="2">
            <quick-cart
              :id="item.product_id"
              :img="item.image"
              :price="item.product_price"
              :title="item.product_name"
              :number="item.product_number"
          /></v-col>
          <v-col cols="12" md="2"
            ><v-tooltip bottom>
              <template #activator="{ on, attrs }">
                <v-btn
                  v-bind="attrs"
                  text
                  elevation="2"
                  v-on="on"
                  @click="unfollowCilck(item.product_id)"
                >
                  <v-icon color="error">mdi-heart-broken-outline</v-icon>
                </v-btn>
              </template>
              <span>เลิกติมตาม</span>
            </v-tooltip></v-col
          >
        </v-row>

        <!-- dialog -->
        <v-dialog v-model="dialog" max-width="300">
          <v-card>
            <v-card-title class="text-h5"> คำเตือน </v-card-title>
            <v-card-text class="text-body-1">
              คุณต้องการเลิกติดตาม
            </v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="green darken-1" text @click="dialog = false">
                ยกเลิก
              </v-btn>
              <v-btn color="red darken-1" text @click="unfollowP"> ตกลง </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <!--  -->
      </template>
    </v-data-table>
    <v-snackbar v-model="snackbar" :timeout="timeout">
      {{ text }}

      <template #action="{ attrs }">
        <v-btn color="blue" text v-bind="attrs" @click="snackbar = false">
          Close
        </v-btn>
      </template>
    </v-snackbar>
  </div>
</template>

<script>
import QuickCart from '~/components/QuickCart.vue'
export default {
  name: 'ProFile',
  components: { QuickCart },
  layout: 'ProfileLayout',
  middleware: 'auth',
  data() {
    return {
      userfollow: [],
      dialog: false,
      search: '',
      page: 1,
      pageCount: 0,
      itemsPerPage: 1,
      headers: [
        {
          text: 'รูปสินค้า',
          value: 'image',
        },
        {
          text: 'ชื่อสินค้า',
          value: 'product_name',
        },
        {
          text: 'ราคา',
          value: 'product_price',
        },
        { text: 'ตัวเลือก', value: 'actions', sortable: false },
      ],
      follow_id: '',
      snackbar: false,
      text: '',
      timeout: 2000,
    }
  },
  async created() {
    await this.getFollow()
  },
  methods: {
    async getFollow() {
      try {
        const respo = await this.$axios.get(`/follow/product`)
        this.userfollow = respo.data
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    unfollowCilck(id) {
      this.dialog = true
      this.follow_id = id
    },
    async unfollowP() {
      try {
        const response = await this.$axios.delete(
          `/follow/product/${this.follow_id}`
        )
        // eslint-disable-next-line no-console
        setTimeout(() => {
          this.snackbar = true
          this.text = 'ยกเลิกติดตามสินค้าแล้ว'
          this.dialog = false
          this.getFollow()
        }, response)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
  },
}
</script>