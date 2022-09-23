<template>
  <div v-if="hasPermission">
    <breadcrumbs :items="breadcrumbs_items" />
    <page-title :item="page_title"/>

    <div class="mb-5">
      <skki-navbar active="hapus"/>

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
              class="rounded-lg"
              @click="delete_dialog = true"
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
        <v-card
          class="rounded-lg"
        >
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
import PrkNavbar from '~/components/PrkNavbar.vue';

const randomstring = require('randomstring')
export default {
  components: {
    Breadcrumbs,
    PageTitle,
    PrkNavbar,
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
    this.$axios.get(`/v1/skki/${this.$route.params.id}`)
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
              text: 'SKKI',
              disabled: false,
              href: '/tahapan/skki/'+this.$route.params.basket,
            },
            {
              text: res.data.data.nomor_prk_skki,
              disabled: true,
              href: '/tahapan/skki/'+this.$route.params.basket+'/'+this.$route.params.id,
            }
          ];

          this.page_title = {
            basket      : res.data.data.basket,
            title       : res.data.data.nomor_prk_skki,
            description : res.data.data.nama_project
          }
        })
  },
  methods: {
    deleteSave() {
      this.$axios.delete(`/v1/skki/${this.id}`)
        .then(res => {
          this.$router.push('/tahapan/skki/1')
        })
        .finally(() => {})
    } 
  },
  computed: {
    hasPermission() {
      return this.$auth.user.permissions.includes('edit-skki') || this.$auth.user.permissions.includes('superadmin');
    }
  }
}
</script>

<style>

</style>