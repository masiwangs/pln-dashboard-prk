<template>
  <div v-if="hasPermission">
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
    <page-title :item="page_title"/>

    <div class="mb-5">
      <pengadaan-navbar active="hapus" :has-permission="hasPermission"/>

      <v-card
        elevation="4"
        class="rounded-lg"
      >
        <v-card-text style="max-height: 500px; overflow-y: auto">
          Hapus project? Data yang telah dihapus tidak dapat dikembalikan.
          <div class="d-flex justify-end">
            <v-btn
              elevation="0"
              dark
              color="red"
              @click="delete_dialog = true"
              class="rounded-lg"
            >Hapus</v-btn>
          </div>
        </v-card-text>
      </v-card>
    </div>

    <v-dialog
        v-model="delete_dialog"
        persistent
        max-width="400"
      >
        <v-card class="rounded-lg">
          <v-card-title class="text-h5">
            Hapus PRK?
          </v-card-title>
          <v-card-text>
              Apakah Anda yakin menghapus SKKI ini? <br/>
              Data yang telah dihapus tidak dapat dikembalikan.
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn
              color="light"
              text
              @click="delete_dialog = false"
              class="rounded-lg"
            >
              Batal
            </v-btn>
            <v-btn
              color="red"
              dark
              elevation="0"
              class="rounded-lg"
              @click="deleteSave"
            >
              Hapus
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
  </div>
</template>

<script>
import Breadcrumbs          from '~/components/header/Breadcrumbs.vue';
import PageTitle            from '~/components/header/PageTitle.vue';
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
      delete_dialog: false,
      snackbar: false,
      snackbar_text: ''
    }
  },
  created() {
    this.id = this.$route.params.id;
  },
  mounted() {
    this.getPengadaan();
  },
  methods: {
    getPengadaan() {
      this.$axios.get(`/v1/pengadaan/${this.$route.params.id}`)
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
              text: 'Pengadaan',
              disabled: false,
              href: '/tahapan/pengadaan/1',
            },
            {
              text: res.data.data.nodin,
              disabled: true,
              href: '/tahapan/pengadaan/'+res.data.data.id,
            }
          ];

          this.page_title = {
            basket      : res.data.data.basket,
            title       : res.data.data.nodin,
            description : res.data.data.nama_project
          }
        })
    },
    deleteSave() {
      this.$axios.delete(`/v1/pengadaan/${this.id}`)
        .then(res => {
          this.$router.push('/tahapan/pengadaan/'+this.$route.params.basket)
        })
        .catch(e => {
          this.snackbar_text = e.response.data.message;
          this.snackbar = true;
        })
        .finally(() => {
          this.delete_dialog = false;
        })
    } 
  },
  computed: {
    hasPermission() {
      return this.$auth.user.permissions.includes('edit-pengadaan') || this.$auth.user.permissions.includes('superadmin');
    }
  }
}
</script>

<style>

</style>