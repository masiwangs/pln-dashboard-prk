<template>
  <div>
    <breadcrumbs :items="breadcrumbs_items" />
    <page-title :item="page_title" />

    <div class="mb-5">
      <pengadaan-navbar active="rab-jasa" :has-permission="hasPermission"/>

      <v-card elevation="4" class="rounded-lg">
        <v-card-title>
          <v-text-field style="max-width: 300px" v-model="search" append-icon="mdi-magnify" label="Cari" single-line
            hide-details dense outlined class="rounded-lg"></v-text-field>
          <v-spacer></v-spacer>
          <v-btn v-if="hasPermission" dark color="light-blue" elevation="0" class="rounded-lg" @click="create_dialog = true">
            BARU
          </v-btn>
        </v-card-title>
        <v-card-title>
          <v-text-field outlined hide-details="auto" dense label="Total WBS Jasa" readonly
            :value="'Rp'+new Intl.NumberFormat('id-ID').format(wbs_stats.total)"
            class="me-sm-2 mb-3 mb-lg-0 rounded-lg"></v-text-field>
          <v-text-field outlined hide-details="auto" dense label="Digunakan WBS Jasa" readonly
            :value="'Rp'+new Intl.NumberFormat('id-ID').format(wbs_stats.used)" class="me-lg-2 mb-3 mb-lg-0 rounded-lg">
          </v-text-field>
          <v-text-field outlined hide-details="auto" dense label="Sisa WBS Jasa" readonly class="rounded-lg"
            :value="'Rp'+new Intl.NumberFormat('id-ID').format(wbs_stats.rest)"></v-text-field>
        </v-card-title>
        <v-data-table :headers="headers" :items="items" :items-per-page="5" class="elevation-0">
          <template v-slot:item.harga="{ item }">
            Rp{{ new Intl.NumberFormat('id-ID').format(item.harga) }}
          </template>
          <template v-slot:item.menu="{ item }">
            <div class="d-flex justify-end">
              <v-btn small dark color="amber" elevation="0" class="rounded-lg me-2" @click="openEditDialog(item.id)">
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
      <v-dialog v-model="create_dialog" persistent max-width="650">
        <v-card elevation="4" class="rounded-lg">
          <v-card-title class="text-h5">
            Tambah Jasa Baru
          </v-card-title>
          <v-card-text>
            <v-row>
              <v-col cols="12" sm="auto">
                <v-text-field outlined class="rounded-lg" hide-details="auto" dense label="Nama Jasa"
                  v-model="create_data[0].nama_jasa">
                </v-text-field>
              </v-col>
              <v-col cols="12" sm="auto">
                <v-text-field outlined class="rounded-lg" hide-details="auto" dense label="Harga" prefix="Rp"
                  v-model="create_data[0].harga" type="number"></v-text-field>
              </v-col>
              <v-col cols="auto" class="d-none d-sm-block">
                <v-btn dark text @click="clearCreateData(0)">
                  <v-icon color="red">mdi-delete</v-icon>
                </v-btn>
              </v-col>
            </v-row>
            <v-row class="d-none d-sm-flex">
              <v-col cols="12" sm="auto">
                <v-text-field outlined class="rounded-lg" hide-details="auto" dense label="Nama Jasa"
                  v-model="create_data[1].nama_jasa">
                </v-text-field>
              </v-col>
              <v-col cols="12" sm="auto">
                <v-text-field outlined class="rounded-lg" hide-details="auto" dense label="Harga" prefix="Rp"
                  v-model="create_data[1].harga" type="number"></v-text-field>
              </v-col>
              <v-col cols="auto">
                <v-btn dark text @click="clearCreateData(1)">
                  <v-icon color="red">mdi-delete</v-icon>
                </v-btn>
              </v-col>
            </v-row>
            <v-row class="d-none d-sm-flex">
              <v-col cols="12" sm="auto">
                <v-text-field outlined class="rounded-lg" hide-details="auto" dense label="Nama Jasa"
                  v-model="create_data[2].nama_jasa">
                </v-text-field>
              </v-col>
              <v-col cols="12" sm="auto">
                <v-text-field outlined class="rounded-lg" hide-details="auto" dense label="Harga" prefix="Rp"
                  v-model="create_data[2].harga" type="number"></v-text-field>
              </v-col>
              <v-col cols="auto">
                <v-btn dark text @click="clearCreateData(2)">
                  <v-icon color="red">mdi-delete</v-icon>
                </v-btn>
              </v-col>
            </v-row>
            <v-row class="d-none d-sm-flex">
              <v-col cols="12" sm="auto">
                <v-text-field outlined class="rounded-lg" hide-details="auto" dense label="Nama Jasa"
                  v-model="create_data[3].nama_jasa">
                </v-text-field>
              </v-col>
              <v-col cols="12" sm="auto">
                <v-text-field outlined class="rounded-lg" hide-details="auto" dense label="Harga" prefix="Rp"
                  v-model="create_data[3].harga" type="number"></v-text-field>
              </v-col>
              <v-col cols="auto">
                <v-btn dark text @click="clearCreateData(3)">
                  <v-icon color="red">mdi-delete</v-icon>
                </v-btn>
              </v-col>
            </v-row>
            <v-row class="d-none d-sm-flex">
              <v-col cols="12" sm="auto">
                <v-text-field outlined class="rounded-lg" hide-details="auto" dense label="Nama Jasa"
                  v-model="create_data[4].nama_jasa">
                </v-text-field>
              </v-col>
              <v-col cols="12" sm="auto">
                <v-text-field outlined class="rounded-lg" hide-details="auto" dense label="Harga" prefix="Rp"
                  v-model="create_data[4].harga" type="number"></v-text-field>
              </v-col>
              <v-col cols="auto">
                <v-btn dark text @click="clearCreateData(4)">
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
            <v-btn color="blue" class="rounded-lg" dark elevation="0" @click="createSave">
              Simpan
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <!-- dialog edit -->
      <v-dialog v-model="edit_dialog" persistent max-width="400">
        <v-card elevation="4" class="rounded-lg">
          <v-card-title class="text-h5">
            Ubah Jasa
          </v-card-title>
          <v-card-text>
            <v-text-field outlined hide-details="auto" dense label="Nama Jasa" :disabled="disabled"
              v-model="edit_data.nama_jasa" class="mb-3 rounded-lg"></v-text-field>
            <v-text-field outlined hide-details="auto" dense label="Harga" :disabled="disabled" class="rounded-lg"
              v-model="edit_data.harga"></v-text-field>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="light" text :disabled="disabled" class="rounded-lg" @click="closeEditDialog">
              Batal
            </v-btn>
            <v-btn color="blue" dark elevation="0" :disabled="disabled" class="rounded-lg" @click="updateSave">
              SIMPAN
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <!-- dialog delete -->
      <v-dialog v-model="delete_dialog" persistent max-width="400">
        <v-card elevation="4" class="rounded-lg">
          <v-card-title class="text-h5">
            Hapus Jasa?
          </v-card-title>
          <v-card-text>
            Apakah Anda yakin menghapus jasa ini? <br />
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
            nama_jasa: '',
            harga: ''
          },
          {
            nama_jasa: '',
            harga: ''
          },
          {
            nama_jasa: '',
            harga: ''
          },
          {
            nama_jasa: '',
            harga: ''
          },
          {
            nama_jasa: '',
            harga: ''
          },
        ],
      }
    },
    created() {
      this.id = this.$route.params.id;
    },
    mounted() {
      this.getData();
      this.getWbsStats();
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
        this.$axios.get(`/v1/pengadaan/${this.id}/jasa`)
          .then(res => {
            this.items = res.data.data
          })
          .finally(() => {})
      },
      getWbsStats() {
        this.$axios.get(`/v1/pengadaan/${this.id}/wbs-jasa`)
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
          nama_jasa: '',
          harga: ''
        }
        this.edit_dialog = false
      },
      updateSave() {
        let data = {
          nama_jasa: this.edit_data.nama_jasa,
          harga: this.edit_data.harga
        }

        this.disabled = true;

        this.$axios.post(`/v1/pengadaan/${this.edit_data.pengadaan_id}/jasa/${this.edit_data.id}`, data)
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
        this.$axios.delete(`/v1/pengadaan/${this.delete_data.pengadaan_id}/jasa/${this.delete_data.id}`)
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
        this.$axios.post(`/v1/pengadaan/${id}/jasa`, data)
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
            nama_jasa: '',
            harga: ''
          },
          {
            nama_jasa: '',
            harga: ''
          },
          {
            nama_jasa: '',
            harga: ''
          },
          {
            nama_jasa: '',
            harga: ''
          },
          {
            nama_jasa: '',
            harga: ''
          },
        ];

        this.create_dialog = false
      },
      clearCreateData(row) {
        this.create_data[row].nama_jasa = '';
        this.create_data[row].harga = '';
      },
    },
    computed: {
      hasPermission() {
        return this.$auth.user.permissions.includes('edit-pengadaan') || this.$auth.user.permissions.includes(
          'superadmin');
      },
      headers() {
        let headers = [{
            text: 'Nama Jasa',
            align: 'start',
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