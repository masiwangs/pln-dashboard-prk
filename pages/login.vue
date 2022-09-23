<template>
  <div class="d-flex justify-center">
    <v-snackbar 
      v-model="snackbar"
    >
      {{ snackbar_text }}
      <template v-slot:action="{ attrs }">
        <v-btn color="blue" text v-bind="attrs" @click="snackbar = false">
          TUTUP
        </v-btn>
      </template>
    </v-snackbar>

    <v-card
      width="300"
      elevation="0"
    >
      <v-card-text>
        <div class="text-center mb-15">
          <img src="https://api-monitoring.masiwang.my.id/images/logo.png" style="width: 5rem" alt="" srcset="">
          <h4 class="black--text">DASHBOARD PRK</h4>
        </div>
        <v-text-field
          outlined
          hide-details="auto"
          dense
          label="Email"
          type="email"
          class="mb-3 rounded-lg"
          v-model="login_data.email"
          :error="login_error.email"
          :error-messages="login_error.email"
        ></v-text-field>
        <v-text-field
          outlined
          hide-details="auto"
          dense
          label="Password"
          type="password"
          class="mb-10 rounded-lg"
          v-model="login_data.password"
          :error="login_error.password"
          :error-messages="login_error.password"
          @keyup.enter="login"
        ></v-text-field>
        <v-btn
          elevation="0"
          dark
          color="light-blue"
          block
          @click="login"
          class="mb-3 rounded-lg"
        >MASUK</v-btn>
        <div class="text-center">
          <p>Lupa kata sandi? <a href="#">Hubungi admin</a></p>
        </div>
      </v-card-text>
    </v-card>
  </div>
</template>

<script>
  export default {
    auth: 'guest',
    layout: 'auth',
    name: 'SigninPage',
    data() {
      return {
        login_data: {
          email: '',
          password: ''
        },
        login_error: {
          email: null,
          password: null
        },

        snackbar: false,
        snackbar_text: '',
        snackbar_timeout: 2000
      }
    },
    methods: {
      login () {
        this.$auth.loginWith('laravelJWT', { data: this.login_data })
          .then(res => {
            this.$auth.setUser(res.data.user)
            this.$auth.setUserToken(res.data.token, 'refreshToken')
          })
          .catch(e => {
            this.snackbar_text = 'Email / password salah. Coba lagi.';
            this.snackbar = true;
            console.log(e)
          })
      }
    }
  }
</script>