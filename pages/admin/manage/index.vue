<template>
  <div>
    <v-row>
      <v-col cols="12">
        <v-card>
          <v-card-text>
            <v-card-title>
              ผู้ใช้งาน
              <v-spacer></v-spacer>
            </v-card-title>

            <v-data-table
              :headers="headers"
              :items="users"
              :search="search"
              no-data-text="ไม่มีข้อมูล"
              class="elevation-1 mt-2"
              :loading="loadingdata"
            >
              <template #top>
                <v-text-field
                  v-model="search"
                  append-icon="mdi-magnify"
                  label="ค้นหาข้อมูล"
                  class="mb-2"
                  single-line
                  hide-details
                ></v-text-field>
              </template>
              <!-- image -->
              <template #[`item.full_name`]="{ item }">
                {{ `${item.f_name} - ${item.l_name}` }}
              </template>
              <template #[`item.created_at`]="{ item }">
                {{
                  $moment(item.created_at)
                    .add(543, 'year')
                    .format('D MMMM YYYY HH:mm:ss')
                }}
              </template>
              <template #[`item.last_login`]="{ item }">
                {{
                  $moment(item.last_login)
                    .add(543, 'year')
                    .format('D MMMM YYYY HH:mm:ss')
                }}
              </template>
              <template #[`item.role`]="{ item }">
                <v-row>
                  <div v-for="val in userrole" :key="val.user_id">
                    <v-col v-if="val.user_id === item.user_id" cols="12">
                      <v-chip class="ma-1" color="primary" outlined label>
                        {{ val.role_name }}
                      </v-chip>
                    </v-col>
                  </div>
                </v-row>
              </template>
              <!-- action -->
              <template #[`item.actions`]="{ item }">
                <v-row>
                  <v-col cols="12" md="6">
                    <v-btn
                      outlined
                      color="warning"
                      @click="openLog(item.user_id)"
                    >
                      <v-icon>mdi-pencil-outline</v-icon>
                    </v-btn>
                  </v-col>
                </v-row>
              </template>
            </v-data-table>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
    <v-dialog v-model="dialog" persistent max-width="350">
      <v-card>
        <v-card-title class="text-h5"> จัดการสิทธิผู้ใช้งาน </v-card-title>
        <v-card-text>
          <v-list-item>
            <v-list-item-content
              ><v-list-item-title> ให้สิทธิเป็นผู้ดูแลระบบ</v-list-item-title>
            </v-list-item-content>
            <v-list-item-action
              ><v-switch v-model="switch1" inset />
            </v-list-item-action>
          </v-list-item>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="error darken-1" text @click="dialog = false">
            ยกเลิก
          </v-btn>
          <v-btn
            color="green darken-1"
            text
            :loading="btnSaveload"
            @click="btnSave"
          >
            ตกลง
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-snackbar v-model="snackbar" :timeout="timeout">
      {{ text }}
      <template #action="{ attrs }">
        <v-btn color="blue" text v-bind="attrs" @click="snackbar = false">
          ปิด
        </v-btn>
      </template>
    </v-snackbar>
  </div>
</template>
  
  <script>
export default {
  name: 'Manage',
  layout: 'AdminLayout',
  data() {
    return {
      users: [],
      userrole: [],
      search: '',
      loadingdata: false,
      headers: [
        {
          text: 'ไอดี',
          value: 'user_id',
        },
        {
          text: 'ชื่อ - นามสกุล',
          value: 'full_name',
        },
        {
          text: 'ชื่อผู้ใช้',
          value: 'username',
        },
        {
          text: 'หน้าที่',
          value: 'role',
        },
        {
          text: 'สร้างเมื่อ',
          value: 'created_at',
        },
        {
          text: 'ใช้งานล่าสุด',
          value: 'last_login',
        },
        { text: 'ตัวเลือก', value: 'actions', sortable: false },
      ],
      user_id: '',
      loadbar: false,
      file: null,
      dialog: false,
      switch1: false,
      isAdmin: false,
      btnSaveload: false,
      //
      snackbar: false,
      timeout: 3000,
      text: '',
    }
  },
  async created() {
    await this.getUser()
    await this.getUserRole()
  },
  methods: {
    async getUser() {
      this.loadingdata = true
      try {
        const respo = await this.$axios.get('/user/all')
        setTimeout(() => {
          this.users = respo.data
          this.loadingdata = false
        }, respo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async getUserRole() {
      try {
        const respo = await this.$axios.get(`/userrole`)
        setTimeout(() => {
          this.userrole = respo.data
          this.loadingdata = false
        }, respo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    openLog(id) {
      this.dialog = true
      this.user_id = id
      this.userrole.forEach((val) => {
        if (this.user_id === val.user_id) {
          if (val.role_id === 2) {
            this.isAdmin = true
            this.switch1 = true
          } else {
            this.isAdmin = false
            this.switch1 = false
          }
        }
      })
    },
    async btnSave() {
      this.btnSaveload = true
      if (this.isAdmin && this.switch1) {
        this.dialog = false
        this.btnSaveload = false
      } else if (this.isAdmin && !this.switch1) {
        try {
          const respo = await this.$axios.delete(
            `/userrole/admin/${this.user_id}`
          )
          setTimeout(async () => {
            await this.getUser()
            await this.getUserRole()
            this.snackbar = true
            this.text = 'อัพเดทข้อมูลแล้ว'
            this.dialog = false
            this.btnSaveload = false
          }, respo)
        } catch (e) {
          // eslint-disable-next-line no-console
          console.log(e)
          this.snackbar = true
          this.text = 'ไม่สามารถข้อมูลแล้ว'
          this.btnSaveload = false
        }
      } else if (!this.isAdmin && this.switch1) {
        try {
          const respo = await this.$axios.post(
            `/userrole/admin`, {
              user_id: this.user_id
            }
          )
          setTimeout(async () => {
            await this.getUser()
            await this.getUserRole()
            this.snackbar = true
            this.text = 'อัพเดทข้อมูลแล้ว'
            this.dialog = false
            this.btnSaveload = false
          }, respo)
        } catch (e) {
          // eslint-disable-next-line no-console
          console.log(e)
          this.snackbar = true
          this.text = 'ไม่สามารถข้อมูลแล้ว'
          this.btnSaveload = false
        }
      } else {
        this.dialog = false
        this.btnSaveload = false
      }
    },
  },
}
</script>