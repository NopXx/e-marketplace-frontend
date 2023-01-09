<template>
  <div class="main-content">
    <v-card elevation="4" outlined max-width="500" min-width="350">
      <v-card-text>
        <h3 class="mx-2">เข้าสู่ระบบ</h3>
        <form @submit.prevent="userLogin">
          <v-container>
            <v-row>
              <v-col cols="12">
                <v-text-field
                  v-model="login.username"
                  label="Username"
                ></v-text-field>
                <v-text-field
                  v-model="login.password"
                  :append-icon="show1 ? 'mdi-eye' : 'mdi-eye-off'"
                  :type="show1 ? 'text' : 'password'"
                  name="input-10-1"
                  label="Password"
                  @click:append="show1 = !show1"
                ></v-text-field>
              </v-col>
            </v-row>
            <v-divider class="mt-2" />
            <div class="mt-4">
              <v-btn
                depressed
                color="primary"
                type="submit"
                :loading="loading"
                width="100%"
              >
                เข้าสู่ระบบ
              </v-btn>
              <v-divider class="mt-3 mb-3" />
              <v-btn to="/auth/signup" color="info" outlined width="100%">
                สมัครใช้งาน
              </v-btn>
            </div>
            <v-snackbar v-model="snackbar">
              {{ errorText }}

              <template #action="{ attrs }">
                <v-btn
                  color="pink"
                  text
                  v-bind="attrs"
                  @click="snackbar = false"
                >
                  ปิด
                </v-btn>
              </template>
            </v-snackbar>
          </v-container>
        </form>
      </v-card-text>
    </v-card>
  </div>
</template>
  
  <script>
export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: 'Login',
  layout: 'LoginLayout',
  data() {
    return {
      login: {
        username: '',
        password: '',
      },
      snackbar: false,
      custom: true,
      loading: false,
      errorText: '',
      show1: false,
    }
  },
  computed: {
    progress() {
      return Math.min(100, this.login.tel.length * 10)
    },
    color() {
      return ['accent', 'warning', 'success'][Math.floor(this.progress / 40)]
    },
  },
  methods: {
    async userLogin(e) {
      e.preventDefault()
      try {
        this.loading = true
        const response = await this.$auth.loginWith('local', {
          data: this.login
        })
        // eslint-disable-next-line no-console
        console.log(response)
        // eslint-disable-next-line no-console
        console.log(this.$auth.user)
        // localStorage.setItem('name', JSON.stringify(response.data.user))
        // await this.$auth.setUser({
        //   name: response.data.name
        // })
        setTimeout(async () => {
          await this.$router.push('/')
          this.loading = false
        }, response)
      } catch (err) {
        this.snackbar = true
        this.loading = false
        // this.expand = true
        // eslint-disable-next-line no-console
        console.log(err.response)
        this.errorText = err.response.data.msg
        // this.error = err.response
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