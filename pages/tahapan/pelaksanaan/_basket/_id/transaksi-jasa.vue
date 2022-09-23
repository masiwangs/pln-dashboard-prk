<template>
  <div>
    <breadcrumbs :items="breadcrumbs_items" />
    <page-title :item="page_title" />

    <div class="mb-5">
      <pelaksanaan-navbar active="transaksi-jasa" :has-permission="hasPermission"/>

      <v-card elevation="4" class="rounded-lg">
        <v-card-title>
          <v-text-field style="max-width: 300px" v-model="search" append-icon="mdi-magnify" label="Cari" single-line
            hide-details dense outlined class="rounded-lg"></v-text-field>
          <v-spacer></v-spacer>
          <v-btn v-if="hasPermission" dark color="light-blue" elevation="0" @click="create_dialog = true" class="rounded-lg">
            BARU
          </v-btn>
        </v-card-title>
        <v-card-title>
          <v-text-field outlined hide-details="auto" dense label="Jasa pada Tahap Kontrak" readonly
            :value="'Rp'+new Intl.NumberFormat('id-ID').format(wbs_stats.total)"
            class="me-sm-2 mb-3 mb-lg-0 rounded-lg"></v-text-field>
          <v-text-field outlined hide-details="auto" dense label="Jasa digunakan" readonly
            :value="'Rp'+new Intl.NumberFormat('id-ID').format(wbs_stats.used)" class="me-lg-2 mb-3 mb-lg-0 rounded-lg">
          </v-text-field>
          <v-text-field :error="wbs_stats.rest < 0" outlined hide-details="auto" dense label="Sisa Jasa" readonly
            class="rounded-lg" :value="'Rp'+new Intl.NumberFormat('id-ID').format(wbs_stats.rest)"></v-text-field>
        </v-card-title>
        <v-data-table :headers="headers" :items="items" :items-per-page="5" class="elevation-0">
          <template v-slot:item.harga="{ item }">
            Rp{{ new Intl.NumberFormat('id-ID').format(item.harga) }}
          </template>
          <template v-slot:item.menu="{ item }">
            <div class="d-flex justify-end">
              <v-btn small dark color="amber" elevation="0" @click="openEditDialog(item.id)" class="me-2 rounded-lg">
                <v-icon>mdi-pencil</v-icon>
              </v-btn>
              <v-btn small dark color="red" elevation="0" class="rounded-lg" @click="openDeleteDialog(item)">
                <v-icon>mdi-delete</v-icon>
              </v-btn>
            </div>
          </template>
        </v-data-table>
      </v-card>

      <!-- dialog baru -->
      <v-dialog v-model="create_dialog" persistent max-width="900">
        <v-card class="rounded-lg">
          <v-card-title class="text-h5">
            Transaksi Jasa Baru
          </v-card-title>
          <v-card-text>
            <v-row>
              <v-col cols="12" sm="4">
                <v-text-field outlined hide-details="auto" dense label="Tanggal" v-model="create_data[0].tanggal"
                  type="date" class="rounded-lg"></v-text-field>
              </v-col>
              <v-col cols="12" sm="4">
                <v-text-field class="rounded-lg" outlined hide-details="auto" dense label="Nama Jasa"
                  v-model="create_data[0].nama_jasa">
                </v-text-field>
              </v-col>
              <v-col cols="12" sm="3">
                <v-text-field outlined class="rounded-lg" hide-details="auto" dense label="Harga" prefix="Rp"
                  v-model="create_data[0].harga" type="number"></v-text-field>
              </v-col>
              <v-col cols="auto" sm="1" class="d-none d-sm-block">
                <v-btn dark text @click="clearCreateData(0)" class="rounded-lg">
                  <v-icon color="red">mdi-delete</v-icon>
                </v-btn>
              </v-col>
            </v-row>
            <v-row class="d-none d-sm-flex">
              <v-col cols="12" sm="4">
                <v-text-field outlined hide-details="auto" class="rounded-lg" dense label="Tanggal" v-model="create_data[1].tanggal"
                  type="date"></v-text-field>
              </v-col>
              <v-col cols="12" sm="4">
                <v-text-field outlined hide-details="auto" class="rounded-lg" dense label="Nama Jasa" v-model="create_data[1].nama_jasa">
                </v-text-field>
              </v-col>
              <v-col cols="12" sm="3">
                <v-text-field outlined hide-details="auto" class="rounded-lg" dense label="Harga" prefix="Rp"
                  v-model="create_data[1].harga" type="number"></v-text-field>
              </v-col>
              <v-col cols="auto" sm="1">
                <v-btn dark text @click="clearCreateData(1)" class="rounded-lg">
                  <v-icon color="red">mdi-delete</v-icon>
                </v-btn>
              </v-col>
            </v-row>
            <v-row class="d-none d-sm-flex">
              <v-col cols="12" sm="4">
                <v-text-field outlined hide-details="auto" dense label="Tanggal" v-model="create_data[2].tanggal"
                  type="date" class="rounded-lg"></v-text-field>
              </v-col>
              <v-col cols="12" sm="4">
                <v-text-field outlined class="rounded-lg" hide-details="auto" dense label="Nama Jasa"
                  v-model="create_data[2].nama_jasa">
                </v-text-field>
              </v-col>
              <v-col cols="12" sm="3">
                <v-text-field outlined class="rounded-lg" hide-details="auto" dense label="Harga" prefix="Rp"
                  v-model="create_data[2].harga" type="number"></v-text-field>
              </v-col>
              <v-col cols="auto" sm="1">
                <v-btn dark text @click="clearCreateData(2)" class="rounded-lg">
                  <v-icon color="red">mdi-delete</v-icon>
                </v-btn>
              </v-col>
            </v-row>
            <v-row class="d-none d-sm-flex">
              <v-col cols="12" sm="4">
                <v-text-field outlined hide-details="auto" class="rounded-lg" dense label="Tanggal" v-model="create_data[3].tanggal"
                  type="date"></v-text-field>
              </v-col>
              <v-col cols="12" sm="4">
                <v-text-field outlined hide-details="auto" class="rounded-lg" dense label="Nama Jasa" v-model="create_data[3].nama_jasa">
                </v-text-field>
              </v-col>
              <v-col cols="12" sm="3">
                <v-text-field outlined hide-details="auto" class="rounded-lg" dense label="Harga" prefix="Rp"
                  v-model="create_data[3].harga" type="number"></v-text-field>
              </v-col>
              <v-col cols="auto" sm="1">
                <v-btn dark text @click="clearCreateData(3)" class="rounded-lg">
                  <v-icon color="red">mdi-delete</v-icon>
                </v-btn>
              </v-col>
            </v-row>
            <v-row class="d-none d-sm-flex">
              <v-col cols="12" sm="4">
                <v-text-field outlined hide-details="auto" class="rounded-lg" dense label="Tanggal" v-model="create_data[4].tanggal"
                  type="date"></v-text-field>
              </v-col>
              <v-col cols="12" sm="4">
                <v-text-field outlined hide-details="auto" class="rounded-lg" dense label="Nama Jasa" v-model="create_data[4].nama_jasa">
                </v-text-field>
              </v-col>
              <v-col cols="12" sm="3">
                <v-text-field outlined hide-details="auto" class="rounded-lg" dense label="Harga" prefix="Rp"
                  v-model="create_data[4].harga" type="number"></v-text-field>
              </v-col>
              <v-col cols="auto" sm="1">
                <v-btn dark text @click="clearCreateData(4)" class="rounded-lg">
                  <v-icon color="red">mdi-delete</v-icon>
                </v-btn>
              </v-col>
            </v-row>

          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="light" class="rounded-lg" text @click="closeCreateDialog">
              Batal
            </v-btn>
            <v-btn color="light-blue" class="rounded-lg" dark elevation="0" @click="createSave">
              Simpan
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <!-- dialog edit -->
      <v-dialog v-model="edit_dialog" persistent max-width="400">
        <v-card class="rounded-lg">
          <v-card-title class="text-h5">
            Ubah Jasa
          </v-card-title>
          <v-card-text>
            <v-text-field outlined hide-details="auto" dense label="Tanggal" :disabled="disabled"
              v-model="edit_data.tanggal" class="mb-3 rounded-lg" type="date"></v-text-field>
            <v-text-field outlined hide-details="auto" dense label="Nama Jasa" :disabled="disabled"
              v-model="edit_data.nama_jasa" class="mb-3 rounded-lg"></v-text-field>
            <v-text-field outlined hide-details="auto" class="rounded-lg" dense label="Harga" :disabled="disabled"
              v-model="edit_data.harga"></v-text-field>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="light" class="rounded-lg" text :disabled="disabled" @click="closeEditDialog">
              Batal
            </v-btn>
            <v-btn color="light-blue" class="rounded-lg" text :disabled="disabled" @click="updateSave">
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
        wbs_stats: {
          total: 0,
          used: 0,
          rest: 0
        },
        create_data: [{
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
        ],
        kontrak: {},
      }
    },
    created() {
      this.id = this.$route.params.id;
    },
    mounted() {
      this.getData();
      this.getKontrak();
      this.getPelaksanaan();
      this.getWbsStats();
    },
    methods: {
      getData() {
        this.$axios.get(`/v1/pelaksanaan/${this.id}/jasa`)
          .then(res => {
            this.items = res.data.data
          })
          .finally(() => {})
      },
      getPelaksanaan() {
        this.$axios.get(`/v1/pelaksanaan/${this.$route.params.id}`)
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
                text: 'Pelaksanaan',
                disabled: false,
                href: '/tahapan/pelaksanaan/1',
              },
              {
                text: res.data.data.spk,
                disabled: true,
                href: '/tahapan/pelaksanaan/' + res.data.data.id,
              }
            ];

            this.page_title = {
              basket: res.data.data.basket,
              title: res.data.data.spk,
              description: res.data.data.pelaksana
            }
          })
      },
      getKontrak() {
        this.$axios.get(`/v1/kontrak/${this.$route.params.id}`)
          .then(res => {
            this.kontrak = res.data.data
          })
          .finally(() => {})
      },
      getWbsStats() {
        this.$axios.get(`/v1/pelaksanaan/${this.id}/wbs-jasa`)
          .then(res => {
            this.wbs_stats = res.data.data
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
        let data = {
          tanggal: this.edit_data.tanggal,
          nama_jasa: this.edit_data.nama_jasa,
          harga: this.edit_data.harga
        }

        this.disabled = true;

        this.$axios.post(`/v1/pelaksanaan/${this.$route.params.id}/jasa/${this.edit_data.id}`, data)
          .then(res => {
            this.getData()
            this.closeEditDialog()
            this.getWbsStats()
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
        this.$axios.delete(`/v1/pelaksanaan/${this.$route.params.id}/jasa/${this.delete_data.id}`)
          .then(res => {
            this.getData()
            this.closeDeleteDialog()
            this.getWbsStats()
          })
          .finally(() => {
            this.disabled = false
          })
      },
      createSave() {
        let id = this.$route.params.id
        let data = this.create_data;
        this.disabled = true;
        this.$axios.post(`/v1/pelaksanaan/${id}/jasa`, data)
          .then(res => {
            this.getData()
            this.closeCreateDialog()
            this.getWbsStats()
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
        return this.$auth.user.permissions.includes('edit-pelaksanaan') || this.$auth.user.permissions.includes('superadmin');
      },
      headers() {
        let headers= [{
            text: 'Tanggal',
            align: 'start',
            value: 'tanggal_formatted',
          },
          {
            text: 'Nama Jasa',
            value: 'nama_jasa',
          },
          {
            text: 'Nilai',
            value: 'harga',
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