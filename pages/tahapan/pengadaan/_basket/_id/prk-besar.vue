<template>
  <div>
    <breadcrumbs :items="breadcrumbs_items" />
    <page-title :item="page_title" />

    <div class="mb-5">
      <pengadaan-navbar active="prk-besar" :has-permission="hasPermission"/>

      <v-card elevation="4" class="rounded-lg">
        <v-card-title>
          <v-text-field style="max-width: 300px" v-model="search" append-icon="mdi-magnify" label="Cari" single-line
            hide-details dense outlined class="rounded-lg"></v-text-field>
          <v-spacer></v-spacer>
          <v-btn v-if="hasPermission" dark color="light-blue" elevation="0" class="rounded-lg" @click="create_dialog = true">Baru</v-btn>
        </v-card-title>
        <v-data-table :headers="headers" :items="items" :items-per-page="5" class="elevation-0">
          <template v-slot:item.skki.nomor_skki="{ item }">
            <nuxt-link :to="'/tahapan/skki/'+item.skki.basket+'/'+item.skki.id">
              {{ item.skki.nomor_skki }}
            </nuxt-link>
          </template>
          <template v-slot:item.rab_jasa="{ item }">
            Rp{{ item.skki.rab_jasa ? new Intl.NumberFormat('id-ID').format(item.skki.rab_jasa.total) : 0 }}
          </template>
          <template v-slot:item.rab_material="{ item }">
            Rp{{ item.skki.rab_material ? new Intl.NumberFormat('id-ID').format(item.skki.rab_material.total) : 0 }}
          </template>
          <template v-slot:item.menu="{ item }">
            <v-btn small dark color="red" elevation="0" @click="openDeleteDialog(item.id)" class="rounded-lg">
              <v-icon>mdi-delete</v-icon>
            </v-btn>
          </template>
        </v-data-table>
      </v-card>

      <!-- dialog baru -->
      <v-dialog v-model="create_dialog" persistent max-width="400">
        <v-card elevation="4" class="rounded-lg">
          <v-card-title class="text-h5">
            Tambah PRK Besar Baru
          </v-card-title>
          <v-card-text>
            <v-autocomplete v-model="create_data" :items="skki_options" outlined dense label="Pilih PRK Besar"
              item-text="nomor_prk_skki" hide-details="auto" item-value="id" class="rounded-lg"></v-autocomplete>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="light" text @click="closeCreateDialog">
              Batal
            </v-btn>
            <v-btn color="blue" dark class="rounded-lg" elevation="0" @click="createSave">
              Simpan
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <!-- dialog delete -->
      <v-dialog v-model="delete_dialog" persistent max-width="400">
        <v-card elevation="4" class="rounded-lg">
          <v-card-title class="text-h5">
            Hapus PRK?
          </v-card-title>
          <v-card-text>
            Apakah Anda yakin menghapus PRK dari SKKI ini? <br />
            Data yang telah dihapus tidak dapat dikembalikan.
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="light" text @click="closeDeleteDialog">
              Batal
            </v-btn>
            <v-btn color="red" dark elevation="0" class="rounded-lg" @click="deleteSave">
              Hapus
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </div>
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

        search: '',
        
        items: [],
        create_dialog: false,
        delete_dialog: false,
        create_data: '',
        disabled: false,
        table_key: '1',
        delete_data: '',
        skki_options: [],
        basket_no: 1
      }
    },
    mounted() {
      this.id = this.$route.params.id;
      this.basket_no = this.$route.params.basket;
      this.getData();
      this.getPrkOptions();
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
                href: '/tahapan/pengadaan/' + res.data.data.id,
              }
            ];

            this.page_title = {
              basket: res.data.data.basket,
              title: res.data.data.nodin,
              description: res.data.data.nama_project
            }
          })
      },
      getData() {
        this.$axios.get(`/v1/pengadaan/${this.id}/skki`)
          .then(res => {
            this.items = res.data.data
          })
          .finally(() => {})
      },
      getPrkOptions() {
        this.$axios.get('/v1/skki?basket=' + this.basket_no)
          .then(res => {
            this.skki_options = res.data.data
          })
          .finally(() => {})
      },
      showToast(type) {
        switch (type) {
          case 'success':
            console.log('success')
            this.snackbar_message = 'Berhasil disimpan!'
            this.show_snackbar = true
            break;

          default:
            break;
        }
      },
      createSave() {
        let id = this.$route.params.id
        let data = {
          skki: this.create_data
        };
        this.disabled = true;
        this.$axios.post(`/v1/pengadaan/${id}/skki`, data)
          .then(res => {
            this.getData()
            this.closeCreateDialog()
          })
          .finally(() => {
            this.disabled = false;
          })
      },
      closeCreateDialog() {
        this.create_data = '';

        this.create_dialog = false
      },
      clearCreateData(index) {
        this.create_data[index].prk_id = '';
      },
      openDeleteDialog(item_id) {
        this.delete_data = item_id;
        this.delete_dialog = true;
      },
      closeDeleteDialog() {
        this.delete_data = '';
        this.delete_dialog = false;
      },
      deleteSave() {
        let pengadaan_id = this.$route.params.id;
        let pengadaan_skki_id = this.delete_data;

        this.disabled = true;
        this.$axios.delete(`/v1/pengadaan/${pengadaan_id}/skki/${pengadaan_skki_id}`)
          .then(res => {
            this.getData()
            this.closeDeleteDialog()
          })
          .finally(() => {
            this.disabled = false
          })
      }
    },
    computed: {
      hasPermission() {
        return this.$auth.user.permissions.includes('edit-pengadaan') || this.$auth.user.permissions.includes(
          'superadmin')
      },
      headers() {
        let headers = [{
            text: 'Nomor SKKI',
            align: 'start',
            value: 'skki.nomor_skki',
          },
          {
            text: 'Nomor PRK-SKKI',
            value: 'skki.nomor_prk_skki',
          },
          {
            text: 'Nomor WBS Jasa',
            value: 'skki.nomor_wbs_jasa',
          },
          {
            text: 'Nomor WBS Material',
            value: 'skki.nomor_wbs_material',
          },
          {
            text: 'WBS Jasa',
            value: 'rab_jasa',
          },
          {
            text: 'WBS Material',
            value: 'rab_material',
          }
        ];

        if(this.hasPermission) {
          headers.push({
            text: '',
            sortable: false,
            align: 'end',
            value: 'menu',
          });
        }

        return headers;
      }
    }
  }
</script>

<style>

</style>