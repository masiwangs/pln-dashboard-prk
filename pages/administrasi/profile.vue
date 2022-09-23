<template>
  <div class="container">
    <!-- snackbar start -->
    <v-snackbar v-model="snackbar">
      {{ snackbar_text }}
      <template v-slot:action="{ attrs }">
        <v-btn color="blue" text v-bind="attrs" @click="snackbar = false">
          TUTUP
        </v-btn>
      </template>
    </v-snackbar>
    <!-- snackbar end -->

    <!-- header start -->
    <div class="d-flex justify-end">
      <v-breadcrumbs :items="breadcrumbItems"></v-breadcrumbs>
    </div>
    <div class="mb-5">
      <page-title title="Profile Anda" description="" />
    </div>
    <!-- header end -->

    <div class="d-flex justify-center">
      <div>
        <v-card width="400" class="mb-5 rounded-lg" elevation="4">
          <v-card-text>
            <div class="text-center">
              <v-avatar size="72" class="mb-5">
                <img :src="user.avatar" alt="" srcset="">
              </v-avatar>
              <h2>{{ user.nama }}</h2>
            </div>
            <v-divider class="my-5"></v-divider>
            <div>
              <v-text-field outlined label="Email" dense hide-details="auto" v-model="user.email" readonly class="mb-3 rounded-lg">
              </v-text-field>
              <v-text-field outlined label="Nama" hide-details="auto" v-model="user.nama" dense class="mb-5 rounded-lg">
              </v-text-field>
              <div class="d-flex justify-end">
                <v-btn elevation="0" dark color="light-blue" class="rounded-lg" @click="updateNama">UPDATE</v-btn>
              </div>
            </div>
          </v-card-text>
        </v-card>
        <v-card width="400" class="rounded-lg" elevation="4">
          <v-card-title>KEAMANAN</v-card-title>
          <v-card-text>
            <div>
              <v-text-field outlined label="Password" dense hide-details="auto" v-model="password.password"
                :error="password_error.password" :error-messages="password_error.password" class="mb-3 rounded-lg" type="password">
              </v-text-field>
              <v-text-field outlined label="Konfirmasi Password" hide-details="auto" v-model="password.confirm_password"
                :error="password_error.confirm_password" :error-messages="password_error.confirm_password" dense
                class="mb-5 rounded-lg" type="password"></v-text-field>
              <div class="d-flex justify-end">
                <v-btn elevation="0" dark class="rounded-lg" color="light-blue" @click="updatePassword">UBAH PASSWORD</v-btn>
              </div>
            </div>
          </v-card-text>
        </v-card>
      </div>
    </div>
  </div>
</template>

<script>
  import PageTitle from '~/components/PageTitle.vue'
  import BasketTab from '~/components/BasketTab.vue'
  export default {
    components: {
      PageTitle,
      BasketTab
    },
    data() {
      return {
        breadcrumbItems: [{
            text: 'Dashboard',
            disabled: false,
            href: '/',
          },
          {
            text: 'Administrasi',
            disabled: false,
            href: '/administrasi/profile',
          },
          {
            text: 'Profil',
            disabled: true,
            href: '/administrasi/profile',
          },
        ],

        snackbar: false,
        snackbar_text: '',

        user: {},
        password: {},
        password_error: {
          password: null,
          confirm_password: null
        }
      }
    },
    mounted() {
      this.user = JSON.parse(JSON.stringify(this.$auth.user));
    },
    methods: {
      updateNama() {
        this.$axios.post(`/auth/user/update/nama`, {
            nama: this.user.nama
          })
          .then(res => {
            this.snackbar_text = 'Nama berhasil diubah.';
            this.snackbar = true;

            this.$auth.fetchUser()
              .then(res => {
                console.log(res)
              })
          })
      },
      updatePassword() {
        this.password_error = {
          password: null,
          confirm_password: null
        }

        this.$axios.post(`/auth/user/update/password`, this.password)
          .then(res => {
            this.snackbar_text = 'Password berhasil diubah.';
            this.snackbar = true;
          })
          .catch(e => {
            this.password_error = e.response.data;
          })
      }
    }
  }
</script>

<style>

</style>