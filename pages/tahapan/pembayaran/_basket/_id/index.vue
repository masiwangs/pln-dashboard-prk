<template>
  <div>
    <!-- snackbar start -->
    <v-snackbar v-model="snackbar" top right>
      {{ snackbar_text }}
      <template v-slot:action="{ attrs }">
        <v-btn color="white" icon v-bind="attrs" @click="snackbar = false">
          <v-icon>mdi-close</v-icon>
        </v-btn>
      </template>
    </v-snackbar>
    <!-- snackbar end -->

    <breadcrumbs :items="breadcrumbs_items" />
    <page-title :item="page_title" />

    <div class="mb-5">
      <pembayaran-navbar active="informasi" :has-permission="hasPermission"/>

      <v-card elevation="4" class="rounded-lg">
        <v-card-text>
          <v-row>
            <v-col cols="12" sm="6" lg="4">
              <v-text-field outlined class="rounded-lg" hide-details="auto" dense label="Nomor Kontrak" readonly
                :value="item.nomor_kontrak"></v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="6" lg="4">
              <v-text-field outlined class="rounded-lg" hide-details="auto" dense label="SE" v-model="item.se" v-debounce="updateData">
              </v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="6" lg="4">
              <v-text-field outlined class="rounded-lg" hide-details="auto" dense label="VIP" v-model="item.vip" v-debounce="updateData">
              </v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="6" lg="4">
              <v-select :items="status_options" class="rounded-lg" label="Status" hide-details="auto" dense v-model="item.status"
                :disabled="disabled" @change="updateData" outlined></v-select>
            </v-col>
          </v-row>
        </v-card-text>
      </v-card>
    </div>
  </div>
</template>

<script>
  import Breadcrumbs from '~/components/header/Breadcrumbs.vue';
  import PageTitle from '~/components/header/PageTitle.vue';
  import PelaksanaanNavbar from '~/components/PelaksanaanNavbar.vue';
  const randomstring = require('randomstring')
  export default {
    components: {
      Breadcrumbs,
      PageTitle,
      PelaksanaanNavbar,
    },

    data() {
      return {
        breadcrumbs_items: [],
        page_title: {},

        item: {},
        disabled: false,
        tab: 'informasi',
        status_options: [{
            text: 'DALAM PELAKSANAAN',
            value: 1
          },
          {
            text: 'ADMINISTRASI PROYEK',
            value: 2
          },
          {
            text: 'OUTSTANDING',
            value: 3
          },
          {
            text: 'SELESAI BAYAR',
            value: 4
          },
        ],
        snackbar: false,
        snackbar_text: ''
      }
    },
    mounted() {
      this.getPembayaran();
      this.getData()
    },
    methods: {
      getPembayaran() {
        this.$axios.get(`/v1/pembayaran/${this.$route.params.id}`)
          .then(res => {
            this.breadcrumbs_items = [{
                text: 'Dashboard',
                disabled: false,
                href: '/',
              },
              {
                text: 'Tahapan',
                disabled: false,
                href: '/tahapan',
              },
              {
                text: 'Pembayaran',
                disabled: false,
                href: '/tahapan/pembayaran/1',
              },
              {
                text: res.data.data.se,
                disabled: true,
                href: '/tahapan/pembayaran/' + res.data.data.id,
              }
            ];

            this.page_title = {
              basket: res.data.data.basket,
              title: res.data.data.se,
              description: res.data.data.pelaksana
            }
          })
      },
      getData() {
        this.$axios.get(`/v1/pembayaran/${this.$route.params.id}`)
          .then(res => {
            this.item = res.data.data
            this.breadcrumbItems[3].text = this.item.nama_project
            this.breadcrumbItems[3].href = '/tahapan/pembayaran/' + this.item.id
          })
          .finally(() => {})
      },
      updateData() {
        if(!this.hasPermission) {
          this.snackbar_text = 'Anda tidak memiliki akses untuk memperbarui pembayaran.';
          this.snackbar = true;
          return this.getData();
        }
        let data = {
          se: this.item.se,
          vip: this.item.vip,
          status: this.item.status
        }
        this.disabled = true;
        this.$axios.post(`/v1/pembayaran/${this.$route.params.id}`, data)
          .then(res => {
            this.snackbar_text = 'Pembayaran berhasil diperbarui.';
            this.snackbar = true;
            return this.getData()
          })
          .catch(e => {
            alert(JSON.stringify(e.response))
          })
          .finally(() => {
            this.disabled = false
          })
      },
    },
    computed: {
      hasPermission() {
        return this.$auth.user.permissions.includes('edit-pembayaran') || this.$auth.user.permissions.includes('superadmin');
      }
    }
  }
</script>

<style>

</style>