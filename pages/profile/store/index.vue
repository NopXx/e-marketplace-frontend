<template>
    <div>
      <v-row>
        <v-col cols="12" md="6">
          <v-card>
            <v-card-text>
              <v-card-title> สมัครร้านค้า </v-card-title>
              <form @submit.prevent="btnSave">
                <v-row>
                  <v-col cols="12">
                    <v-text-field
                      v-model="StoreData.store_name"
                      label="ชื่อร้าน"
                      prepend-icon="mdi-account-outline"
                      required
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12">
                    <v-text-field
                      v-model="StoreData.store_username"
                      label="ชื่อผู้ใช้"
                      prepend-icon="mdi-account-outline"
                      required
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12">
                    <v-alert v-if="text !== ''" dense text :type="type">
                      {{ text }}
                    </v-alert>
                  </v-col>
                  <v-card-actions>
                    <v-btn outlined color="primary" type="submit">
                      สมัครร้านค้า
                    </v-btn>
                  </v-card-actions>
                </v-row>
              </form>
            </v-card-text>
          </v-card>
        </v-col>
        <v-col cols="12" md="6"> </v-col>
      </v-row>
    </div>
  </template>
  
  <script>
  export default {
    layout: 'ProfileLayout',
    middleware: 'auth',
    data() {
      return {
        StoreData: {
          store_name: '',
          store_username: '',
        },
        text: '',
        type: '',
      }
    },
    async created() {
      await this.getRole()
    },
    methods: {
      async getRole() {
        try {
          const respo = await this.$axios.get(`role`)
  
          respo.data.forEach((val) => {
            if (val.store_id !== null) {
              this.$router.push('/store')
            }
          })
        } catch (e) {
          // eslint-disable-next-line no-console
          console.log(e)
        }
      },
      async btnSave() {
          try {
              const respo = await this.$axios.post('/store', this.StoreData)
              setTimeout(() => {
                  this.text = "สมัครร้านค้าสำเร็จ"
                  this.type = 'success'
              }, respo);
          } catch (e) {
              // eslint-disable-next-line no-console
              this.text = "มีชื่อผู้ใช้นี้แล้ว"
              this.type = 'error'
          }
      }
    },
  }
  </script>