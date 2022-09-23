<template>
  <div>
    <breadcrumbs :items="breadcrumbs_items" />
    <page-title :item="page_title" />

    <div class="mb-5">
      <pembayaran-navbar active="pembayaran" :has-permission="hasPermission"/>

      <v-card elevation="0" outlined>
        <v-card-title>
          <v-text-field dense outlined class="rounded-lg" style="max-width: 300px" v-model="search" append-icon="mdi-magnify" label="Cari" single-line
            hide-details></v-text-field>
          <v-spacer></v-spacer>
          <v-btn v-if="hasPermission" dark class="rounded-lg" color="light-blue" elevation="0" @click="create_dialog = true">
            BARU
          </v-btn>
        </v-card-title>
        <v-card-title>
          <v-text-field outlined hide-details="auto" dense label="Tagihan" readonly
            :value="'Rp'+new Intl.NumberFormat('id-ID').format(stats.total)" class="me-sm-2 mb-3 mb-lg-0 rounded-lg">
          </v-text-field>
          <v-text-field outlined hide-details="auto" dense label="Jumlah Dibayar" readonly
            :value="'Rp'+new Intl.NumberFormat('id-ID').format(stats.used)" class="me-lg-2 mb-3 mb-lg-0 rounded-lg"></v-text-field>
          <v-text-field :error="stats.rest < 0" outlined hide-details="auto" dense label="Sisa Tagihn" class="rounded-lg" readonly
            :value="'Rp'+new Intl.NumberFormat('id-ID').format(stats.rest)"></v-text-field>
        </v-card-title>
        <v-data-table :headers="headers" :items="items" :items-per-page="5" class="elevation-0">
          <template v-slot:item.nominal="{ item }">
            Rp{{ new Intl.NumberFormat('id-ID').format(item.nominal) }}
          </template>
          <template v-slot:item.menu="{ item }">
            <div class="d-flex justify-end">
              <v-btn small dark color="amber" class="rounded-lg me-2" elevation="0" @click="openEditDialog(item.id)">
                <v-icon>mdi-pencil</v-icon>
              </v-btn>
              <v-btn small dark color="red" class="rounded-lg" elevation="0" @click="openDeleteDialog(item)">
                <v-icon>mdi-delete</v-icon>
              </v-btn>
            </div>
          </template>
        </v-data-table>
      </v-card>

      <!-- create dialog -->
      <v-dialog v-model="create_dialog" persistent max-width="400">
        <v-card class="rounded-lg">
          <v-card-title class="text-h5">
            Pembayaran Baru
          </v-card-title>
          <v-card-text>
            <v-text-field outlined hide-details="auto" dense label="Tanggal" :disabled="disabled"
              v-model="create_data.tanggal" class="mb-3 rounded-lg" type="date"></v-text-field>
            <v-text-field outlined hide-details="auto" dense label="Nominal" type="number" prefix="Rp"
              v-model="create_data.nominal" class="mb-3 rounded-lg"></v-text-field>
            <v-textarea class="rounded-lg" outlined hide-details="auto" label="Keterangan" v-model="create_data.keterangan"></v-textarea>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="light" class="rounded-lg" text :disabled="disabled" @click="closeCreateDialog">
              Batal
            </v-btn>
            <v-btn color="blue" class="rounded-lg" dark elevation="0" :disabled="disabled" @click="createSave">
              SIMPAN
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <!-- dialog edit -->
      <v-dialog v-model="edit_dialog" persistent max-width="400">
        <v-card class="rounded-lg">
          <v-card-title class="text-h5">
            Ubah Pembayaran
          </v-card-title>
          <v-card-text>
            <v-text-field outlined hide-details="auto" dense label="Tanggal" :disabled="disabled"
              v-model="edit_data.tanggal" class="mb-3 rounded-lg" type="date"></v-text-field>
            <v-text-field outlined hide-details="auto" dense label="Nominal" :disabled="disabled"
              v-model="edit_data.nominal" type="number" prefix="Rp" class="mb-3 rounded-lg"></v-text-field>
            <v-textarea class="rounded-lg" outlined hide-details="auto" label="Keterangan" v-model="edit_data.keterangan"></v-textarea>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="light" text :disabled="disabled" @click="closeEditDialog">
              Batal
            </v-btn>
            <v-btn color="light-blue" dark elevation="0" class="rounded-lg" :disabled="disabled" @click="updateSave">
              SIMPAN
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <!-- dialog delete -->
      <v-dialog v-model="delete_dialog" persistent max-width="400">
        <v-card class="rounded-lg">
          <v-card-title class="text-h5">
            Hapus Jasa?
          </v-card-title>
          <v-card-text>
            Apakah Anda yakin menghapus jasa ini? <br />
            Data yang telah dihapus tidak dapat dikembalikan.
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="light" class="rounded-lg" text @click="closeDeleteDialog">
              Batal
            </v-btn>
            <v-btn color="red" class="rounded-lg" dark elevation="0" @click="deleteSave">
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
  import PembayaranNavbar from '~/components/PembayaranNavbar.vue';

  const randomstring = require('randomstring')
  export default {
    components: {
      Breadcrumbs,
      PageTitle,
      PembayaranNavbar,
    },

    data() {
      return {
        breadcrumbs_items: [],
        page_title: {},

        search: '',
        
        items: [],
        create_dialog: false,
        edit_dialog: false,
        delete_dialog: false,
        edit_data: {
          id: '',
          nama_jasa: '',
          harga_jasa: ''
        },
        disabled: false,
        table_key: '1',
        delete_data: '',
        stats: {
          total: 0,
          used: 0,
          rest: 0
        },
        create_data: {},
        kontrak: {},
      }
    },
    created() {
      this.id = this.$route.params.id;
    },
    mounted() {
      this.getData();
      this.getPembayaran();
      this.getStats();
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
        this.$axios.get(`/v1/pembayaran/${this.$route.params.id}/pembayaran`)
          .then(res => {
            this.items = res.data.data
          })
          .finally(() => {})
      },
      getStats() {
        this.$axios.get(`/v1/pembayaran/${this.id}/stats`)
          .then(res => {
            this.stats = res.data.data
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
      openEditDialog(item_id) {
        const jasa = this.items.find(x => x.id == item_id)
        this.edit_data = jasa;
        this.edit_dialog = true
      },
      closeEditDialog() {
        this.edit_data = {
          id: '',
          tanggal: '',
          nama_jasa: '',
          harga: ''
        }
        this.edit_dialog = false
      },
      updateSave() {
        this.disabled = true;

        this.$axios.post(`/v1/pembayaran/${this.$route.params.id}/pembayaran/${this.edit_data.id}`, this.edit_data)
          .then(res => {
            this.getData()
            this.getStats()
            this.closeEditDialog()
          })
          .finally(() => {
            this.disabled = false;
          })
      },
      openDeleteDialog(item) {
        this.delete_data = item;
        this.delete_dialog = true;
      },
      closeDeleteDialog() {
        this.delete_data = '';
        this.delete_dialog = false;
      },
      deleteSave() {
        this.disabled = true;
        this.$axios.delete(`/v1/pembayaran/${this.$route.params.id}/pembayaran/${this.delete_data.id}`)
          .then(res => {
            this.getData()
            this.getStats()
            this.closeDeleteDialog()
          })
          .finally(() => {
            this.disabled = false
          })
      },
      createSave() {
        let id = this.$route.params.id
        let data = this.create_data;
        this.disabled = true;
        this.$axios.post(`/v1/pembayaran/${id}/pembayaran`, data)
          .then(res => {
            this.getData()
            this.getStats()
            this.closeCreateDialog()
          })
          .finally(() => {
            this.disabled = false;
          })
      },
      closeCreateDialog() {
        this.create_data = [{
            tanggal: '',
            nama_jasa: '',
            harga: ''
          },
          {
            tanggal: '',
            nama_jasa: '',
            harga: ''
          },
          {
            tanggal: '',
            nama_jasa: '',
            harga: ''
          },
          {
            tanggal: '',
            nama_jasa: '',
            harga: ''
          },
          {
            tanggal: '',
            nama_jasa: '',
            harga: ''
          },
        ];

        this.create_dialog = false
      },
      clearCreateData(row) {
        this.create_data[row].tanggal = '';
        this.create_data[row].nama_jasa = '';
        this.create_data[row].harga = '';
      },
    },
    computed: {
      hasPermission() {
        return this.$auth.user.permissions.includes('edit-pembayaran') || this.$auth.user.permissions.includes('superadmin');
      },
      headers() {
        let headers = [{
            text: 'Tanggal',
            align: 'start',
            value: 'tanggal_formatted',
          },
          {
            text: 'Nominal',
            value: 'nominal',
          },
          {
            text: 'Keterangan',
            value: 'keterangan',
          }
        ];
        if(this.hasPermission) {
          headers.push({
            text: '',
            sortable: false,
            align: 'right',
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