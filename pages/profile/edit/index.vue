<template>
  <div>
    <v-row justify="start">
      <v-col cols="12" md="4">
        <v-card elevation="2" outlined>
          <v-card-text>
            <v-avatar size="128">
              <v-img
                v-if="user.image === null"
                src="https://res.cloudinary.com/dqolakmsp/image/upload/v1676141396/image/profile/profile_sgnfpv.png"
                contain
              ></v-img>
              <v-img
                v-else
                :src="user.image"
                aspect-ratio="1.4"
                max-height="125"
                max-width="110"
                contain
              ></v-img>
            </v-avatar>
            <v-card-title>
              {{ `${user.f_name} - ${user.l_name}` }}
            </v-card-title>
            <v-card-title>
              {{ user.tel }}
            </v-card-title>
            <div class="buttons mt-5">
              <v-btn
                color="primary"
                elevation="2"
                class="primary1"
                @click="dialog = !dialog"
                >แก้ไขข้อมูล</v-btn
              >
            </div>
          </v-card-text>
        </v-card>
        <v-dialog v-model="dialog" persistent max-width="600px">
          <v-card>
            <v-card-title>
              <span class="text-h5">แก้ไขข้อมูล</span>
            </v-card-title>
            <v-card-text>
              <v-container>
                <v-row>
                  <v-avatar class="profile" color="grey" size="164">
                    <v-img
                      v-if="user.image === null"
                      src="https://res.cloudinary.com/dqolakmsp/image/upload/v1676141396/image/profile/profile_sgnfpv.png"
                      contain
                    ></v-img>
                    <v-img v-else :src="user.image" contain></v-img>
                  </v-avatar>
                  <v-col cols="12" sm="10">
                    <v-file-input
                      v-model="file"
                      truncate-length="15"
                      label="อัพโหลดรูป"
                    ></v-file-input>
                    <v-progress-linear
                      v-show="loadbar"
                      indeterminate
                      color="green"
                    ></v-progress-linear>
                  </v-col>
                  <v-col cols="12" sm="10">
                    <v-btn
                      outlined
                      color="primary"
                      :disabled="file !== '' ? false : true"
                      @click="uploadfile"
                      >อัพโหลดรูป</v-btn
                    >
                  </v-col>
                  <v-col cols="12" sm="6">
                    <v-text-field
                      v-model="userData.f_name"
                      label="ชื่อ"
                      required
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6">
                    <v-text-field
                      v-model="userData.l_name"
                      label="นามสกุล"
                      required
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6">
                    <v-text-field
                      v-model="userData.username"
                      label="ชื่อผู้ใช้"
                      required
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6">
                    <v-text-field
                      v-model="userData.password"
                      label="รหัสผ่าน"
                      type="password"
                      required
                    ></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="error" text @click="dialog = false"> ยกเลิก </v-btn>
              <v-btn
                color="primary"
                elevation="2"
                :loading="loading"
                @click="submitData()"
              >
                บันทึก
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-col>
      <v-col cols="12" md="8">
        <user-add />
        <v-row>
          <v-col cols="12"> </v-col>
        </v-row>
      </v-col>
    </v-row>
    <v-snackbar
      v-model="snackbar"
      :timeout="timeout"
      absolute
      bottom
      :color="colors"
      outlined
    >
      {{ text }}
      <template #action="{ attrs }">
        <v-btn color="error" v-bind="attrs" @click="snackbar = false">
          <v-icon>mdi-close</v-icon>
        </v-btn>
      </template>
    </v-snackbar>
  </div>
</template>

<script>
import UserAdd from '~/components/ProfileEdit/UserAdd.vue'
export default {
  name: 'ProFile',
  components: { UserAdd },
  layout: 'ProfileLayout',
  middleware: 'auth',
  data: () => ({
    user: [],
    snackbar: false,
    text: '',
    timeout: 2000,
    loading: false,
    colors: '',
    custom: true,
    userData: {
      f_name: '',
      l_name: '',
      tel: '',
      username: '',
      password: '',
    },
    dialog: false,
    file: '',
    loadbar: false,
  }),
  computed: {
    progress() {
      return Math.min(100, this.userData.tel.length * 10)
    },
    color() {
      return ['accent', 'warning', 'success'][Math.floor(this.progress / 40)]
    },
  },
  created() {
    this.getUserData()
    // eslint-disable-next-line no-console
    console.log(this.$auth.user)
  },
  methods: {
    async getUserData() {
      try {
        const response = await this.$axios.get(`/user`)

        // eslint-disable-next-line no-console
        response.data.forEach((val) => {
          this.user = val
          this.userData.f_name = val.f_name
          this.userData.l_name = val.l_name
          this.userData.tel = val.tel
          this.userData.username = val.username
          this.userData.password = val.password
        })
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    async submitData() {
      this.loading = true
      try {
        const response = await this.$axios.patch(
          `/user/${this.$auth.user.user_id}`,
          {
            f_name: this.userData.f_name,
            l_name: this.userData.l_name,
            username: this.userData.username,
            password: this.userData.password,
          }
        )
        // eslint-disable-next-line no-console
        setTimeout(() => {
          this.snackbar = true
          this.loading = false
          this.text = 'บันทึกข้อมูลแล้ว'
          this.colors = 'success'
          this.getUserData()
          this.dialog = false
        }, response)
      } catch (e) {
        // eslint-disable-next-line no-console
        this.snackbar = true
        this.loading = false
        this.colors = 'error'
        this.text = 'error'
      }
      try {
        const resp = this.$auth.loginWith('local', {
          data: {
            username: this.userData.username,
            password: this.userData.password,
          },
        })
        setTimeout(() => {
          location.reload()
        }, resp)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.error(e)
      }
    },
    async uploadfile() {
      this.loadbar = true
      try {
        const img = this.file
        const formData = new FormData()
        formData.append('image', img)
        this.loading1 = true
        const response = await this.$axios.post('/upload/profile', formData)
        setTimeout(() => {
          this.getUserData()
          this.file = {}
          this.loadbar = false
        }, response)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
        this.loadbar = false
      }
    },
  },
}
</script>

<style scoped>
h3 {
  margin: 10px 0;
}
h4 {
  margin: 5px 0;
  text-transform: uppercase;
}
p {
  font-size: 14px;
  line-height: 21px;
}
.card-container {
  padding: 1.5em;
  padding-top: 30px;
  position: relative;
  width: 100%;
  max-width: 100%;
  text-align: center;
}
.card-container .pro {
  color: #231e39;
  background-color: #febb0b;
  border-radius: 3px;
  font-size: 14px;
  font-weight: bold;
  padding: 3px 7px;
  position: absolute;
  top: 30px;
  left: 30px;
}
.card-container .round {
  border-radius: 50%;
  margin: 15px;
  padding: 2px;
}
button.primary1 {
  font-family: Montserrat, sans-serif;
  font-weight: 500;
  padding: 10px 25px;
}
button.primary1.ghost {
  background-color: transparent;
  color: #02899c;
}
.skills {
  background-color: #1f1a36;
  text-align: left;
  padding: 15px;
  margin-top: 30px;
}
.skills ul {
  list-style-type: none;
  margin: 0;
  padding: 0;
}
.skills ul li {
  border: 1px solid #2d2747;
  border-radius: 2px;
  display: inline-block;
  font-size: 12px;
  margin: 0 7px 7px 0;
  padding: 7px;
}
</style>