<template>
  <div>
    <breadcrumbs :items="breadcrumbs_items" />
    <page-title :item="page_title" />

    <div class="mb-5">
      <pembayaran-navbar active="hapus" :has-permission="hasPermission"/>

      <v-card elevation="4" class="rounded-lg">
        <v-card-text style="max-height: 500px; overflow-y: auto">
          Hapus project? Data yang telah dihapus tidak dapat dikembalikan.
          <div class="d-flex justify-end">
            <v-btn elevation="0" class="rounded-lg" dark color="red" @click="delete_dialog = true">Hapus</v-btn>
          </div>
        </v-card-text>
      </v-card>
    </div>

    <v-dialog v-model="delete_dialog" persistent max-width="400">
      <v-card class="rounded-lg">
        <v-card-title class="text-h5">
          Hapus PRK?
        </v-card-title>
        <v-card-text>
          Apakah Anda yakin menghapus SKKI ini? <br />
          Data yang telah dihapus tidak dapat dikembalikan.
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="light" text @click="delete_dialog = false" class="rounded-lg">
            Batal
          </v-btn>
          <v-btn color="red" class="rounded-lg" dark elevation="0" @click="deleteSave">
            Hapus
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
  import Breadcrumbs from '~/components/header/Breadcrumbs.vue';
  import PageTitle from '~/components/header/PageTitle.vue';
  import PengadaanNavbar from '~/components/PengadaanNavbar.vue';

  const randomstring = require('randomstring')
  export default {
    components: {
      Breadcrumbs,
      PageTitle,
      PengadaanNavbar,
    },

    data() {
      return {
        breadcrumbs_items: [],
        page_title: {},

        delete_dialog: false
      }
    },
    created() {
      this.id = this.$route.params.id;
    },
    mounted() {
      this.getPembayaran();
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
      deleteSave() {
        this.$axios.delete(`/v1/pengadaan/${this.id}`)
          .then(res => {
            this.$router.push('/tahapan/pengadaan/' + this.$route.params.basket)
          })
          .finally(() => {})
      }
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