<template>
  <div class="main-content">
    <v-card elevation="4" outlined max-width="500" min-width="350">
      <v-card-text>
        <h3 class="mx-2">เข้าสู่ระบบ</h3>
        <form @submit.prevent="Singup">
          <v-container>
            <v-row>
              <v-col cols="12" md="6">
                <v-text-field
                  v-model="singup.f_name"
                  label="ชื่อ"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12" md="6">
                <v-text-field
                  v-model="singup.l_name"
                  label="นามสกุล"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12" md="6">
                <v-text-field
                  v-model="singup.username"
                  label="Username"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12" md="6">
                <v-text-field
                  v-model="singup.password"
                  type="password"
                  label="Password"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12">
                <v-text-field
                  v-model="singup.tel"
                  label="เบอร์โทรศัพท์"
                  counter="10"
                  oninput="javascript: if (this.value.length > this.maxLength) this.value = this.value.slice(0, this.maxLength);"
                  maxlength="10"
                  type="number"
                  loading
                  required
                >
                  <template #progress>
                    <v-progress-linear
                      v-if="custom"
                      :value="progress"
                      :color="color"
                      absolute
                      rounded
                      height="5"
                    ></v-progress-linear>
                  </template>
                </v-text-field>
              </v-col>
            </v-row>
            <v-divider class="mt-2" />
            <div class="mt-4">
              <v-btn
                depressed
                color="primary"
                type="submit"
                :loading="loading"
                :disabled="singup.tel.length === 10 ? false : true"
                width="100%"
              >
                สมัคร
              </v-btn>
              <v-divider class="mt-3 mb-3" />
              <v-btn to="/auth/" color="error" outlined width="100%">
                ยกเลิก
              </v-btn>
            </div>
            <v-col v-if="errorStatus === false" v-show="alert" cols="12">
              <v-alert elevation="2" colored-border icon="mdi-check-all">
                <v-row>
                  <v-col class="grow">
                    <p>
                      {{
                        errorMessage === '' ? 'สร้างบัญชีสำเร็จ' : errorMessage
                      }}
                    </p>
                  </v-col>
                  <v-col class="shrink">
                    <v-btn
                      color="primary"
                      :loading="loading1"
                      @click="isBtnLogin()"
                      >เข้าสู่ระบบ</v-btn
                    >
                  </v-col>
                </v-row>
              </v-alert>
            </v-col>

            <v-col v-else v-show="alert" cols="12">
              <v-alert
                color="error"
                border="left"
                elevation="2"
                colored-border
                icon="mdi-alert-circle"
              >
                <v-row>
                  <v-col class="grow">
                    {{
                      `${errorMessage.data.message} : ${errorMessage.status}`
                    }}</v-col
                  >
                </v-row>
              </v-alert>
            </v-col>
          </v-container>
        </form>
      </v-card-text>
    </v-card>
  </div>
</template>

<script>
export default {
  name: 'SignUp',
  data() {
    return {
      singup: {
        f_name: '',
        l_name: '',
        username: '',
        password: '',
        tel: '',
      },
      custom: true,
      loading: false,
      loading1: false,
      errorMessage: '',
      alert: false,
      mesSingup: 'Singup',
      errorStatus: false,
    }
  },
  computed: {
    progress() {
      return Math.min(100, this.singup.tel.length * 10)
    },
    color() {
      return ['accent', 'warning', 'success'][Math.floor(this.progress / 40)]
    },
  },
  methods: {
    async Singup() {
      this.loading = true
      try {
        const response = await this.$axios.post('/auth/sign-up', {
          f_name: this.singup.f_name,
          l_name: this.singup.l_name,
          username: this.singup.username,
          password: this.singup.password,
          tel: this.singup.tel,
        })
        // eslint-disable-next-line no-console
        console.log(response)
        setTimeout(() => {
          this.alert = true
          this.mesSingup = 'OK'
          this.loading = false
        }, 3000)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e.response)
        this.errorMessage = e.response
        this.errorStatus = true
        this.alert = true
        this.loading = false
      }
    },
    async isBtnLogin() {
      this.loading1 = true
      const payload = {
        username: this.singup.username,
        password: this.singup.password,
      }
      try {
        const response = await this.$auth.loginWith('local', {
          data: payload,
        })
        // eslint-disable-next-line no-console
        console.log(response)
        setTimeout(() => {
          this.loading1 = false
          this.$router.push('/')
        }, 2000)
      } catch (err) {
        // eslint-disable-next-line no-console
        console.log(err)
        this.loading1 = false
      }
    },
  },
}
</script>

<style>
.main-content {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 5em;
  margin-bottom: auto;
}
</style>