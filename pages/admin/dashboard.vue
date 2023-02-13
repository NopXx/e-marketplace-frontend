<template>
  <div>
    <v-row>
      <v-col cols="12" md="4">
        <v-card>
          <v-card-text>
            <v-card-title> ผู้ใช้ทั้งหมด {{ user_n }} </v-card-title>
            <v-list>
              <!-- order ok -->
              <v-list-item>
                <v-list-item-icon>
                  <v-icon color="primary">mdi-account-outline</v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>ผู้ใช้ทั่วไป</v-list-item-title>
                  <v-list-item-subtitle>{{ user_n }}</v-list-item-subtitle>
                </v-list-item-content>
              </v-list-item>
              <!-- order wait -->
              <v-list-item>
                <v-list-item-icon>
                  <v-icon color="green">mdi-storefront-outline</v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>ร้านค้า</v-list-item-title>
                  <v-list-item-subtitle>{{ store_n }}</v-list-item-subtitle>
                </v-list-item-content>
              </v-list-item>
              <!-- order cancel -->
              <v-list-item>
                <v-list-item-icon>
                  <v-icon color="info">mdi-shield-crown-outline</v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>Admin</v-list-item-title>
                  <v-list-item-subtitle>{{ admin_n }}</v-list-item-subtitle>
                </v-list-item-content>
              </v-list-item>
            </v-list>
          </v-card-text>
        </v-card>
      </v-col>
      <v-col cols="12" md="4">
        <v-card>
          <v-card-text>
            <v-card-title>
              คำสั่งซื้อทั้งหมด {{ orderData.length }}
            </v-card-title>
            <v-list>
              <!-- order ok -->
              <v-list-item>
                <v-list-item-icon>
                  <v-icon color="green">mdi-check-circle-outline</v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>คำสั่งที่สำเร็จ</v-list-item-title>
                  <v-list-item-subtitle>{{ order_ok }}</v-list-item-subtitle>
                </v-list-item-content>
              </v-list-item>
              <!-- order wait -->
              <v-list-item>
                <v-list-item-icon>
                  <v-icon color="warning">mdi-clock-outline</v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>คำสั่งที่กำลังดำเนินการ</v-list-item-title>
                  <v-list-item-subtitle>{{ order_wait }}</v-list-item-subtitle>
                </v-list-item-content>
              </v-list-item>
              <!-- order cancel -->
              <v-list-item>
                <v-list-item-icon>
                  <v-icon color="error">mdi-close-circle-outline</v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>คำสั่งที่ยกเลิก</v-list-item-title>
                  <v-list-item-subtitle>{{
                    order_cancel
                  }}</v-list-item-subtitle>
                </v-list-item-content>
              </v-list-item>
            </v-list>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<script>
export default {
  name: 'AdminDashboard',
  layout: 'AdminLayout',
  data() {
    return {
      user_n: 0,
      store_n: 0,
      admin_n: 0,
      userRoleData: '',
      order_ok: 0,
      order_wait: 0,
      order_cancel: 0,
      orderData: '',
    }
  },
  async created() {
    await this.getUserRole()
    await this.getOrder()
  },
  methods: {
    async getUserRole() {
      try {
        const respo = await this.$axios.get('/userrole')
        respo.data.forEach((val) => {
          if (val.role_name === 'admin') {
            this.admin_n += 1
          } else if (val.role_name === 'user') {
            this.user_n += 1
          } else {
            this.store_n += 1
          }
        })
        this.userRoleData = respo.data
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async getOrder() {
      try {
        const respo = await this.$axios.get('/order/all')
        respo.data.forEach((val) => {
          if (val.order_user_cancel === 1 || val.order_store_cancel === 1) {
            this.order_cancel += 1
          } else if (
            val.order_status === 0 &&
            val.order_user_cancel === 0 &&
            val.order_store_cancel === 0
          ) {
            this.order_wait += 1
          } else if (
            val.order_status === 3 &&
            val.order_user_cancel === 0 &&
            val.order_store_cancel === 0
          ) {
            this.order_ok += 1
          }
        })
        this.orderData = respo.data
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
  },
}
</script>