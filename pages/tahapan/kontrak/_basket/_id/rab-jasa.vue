<template>
  <div>
    <breadcrumbs :items="breadcrumbs_items" />
    <page-title :item="page_title" />

    <div class="mb-5">
      <kontrak-navbar active="rab-jasa" :has-permission="hasPermission"/>

      <v-card elevation="4" class="rounded-lg">
        <v-card-title>
          <v-text-field style="max-width: 300px" v-model="search" append-icon="mdi-magnify" label="Cari" single-line
            hide-details class="rounded-lg"></v-text-field>
          <v-spacer></v-spacer>
          <v-btn v-if="kontrak.is_amandemen" dark color="light-blue" elevation="0" @click="create_dialog = true"
            class="rounded-lg">
            BARU
          </v-btn>
        </v-card-title>
        <v-card-title>
          <v-text-field outlined hide-details="auto" dense label="Total WBS Jasa di Pengadaan" readonly
            :value="'Rp'+new Intl.NumberFormat('id-ID').format(wbs_stats.total)"
            class="me-sm-2 mb-3 mb-lg-0 rounded-lg"></v-text-field>
          <v-text-field outlined hide-details="auto" dense label="Total Digunakan WBS Jasa di Kontrak" readonly
            :value="'Rp'+new Intl.NumberFormat('id-ID').format(wbs_stats.used)" class="me-lg-2 mb-3 mb-lg-0 rounded-lg">
          </v-text-field>
          <v-text-field :error="wbs_stats.rest < 0" outlined hide-details="auto" dense label="Sisa WBS Jasa"
            class="rounded-lg" readonly :value="'Rp'+new Intl.NumberFormat('id-ID').format(wbs_stats.rest)">
          </v-text-field>
        </v-card-title>
        <v-data-table :headers="headers" :items="items" :items-per-page="5" class="elevation-0">
          <template v-slot:item.harga="{ item }">
            Rp{{ new Intl.NumberFormat('id-ID').format(item.harga) }}
          </template>
          <template v-slot:item.menu="{ item }">
            <div class="d-flex justify-end">
              <v-btn v-if="kontrak.is_amandemen == 1" small dark color="amber" elevation="0"
                @click="openEditDialog(item.id)" class="me-2 rounded-lg">
                <v-icon>mdi-pencil</v-icon>
              </v-btn>
              <v-btn v-if="kontrak.is_amandemen == 1" small dark color="red" elevation="0" class="rounded-lg"
                @click="openDeleteDialog(item)">
                <v-icon>mdi-delete</v-icon>
              </v-btn>
            </div>
          </template>
        </v-data-table>
        <v-card-text v-if="hasPermission">
          <v-row>
            <v-col cols="12">
              <v-divider></v-divider>
            </v-col>
          </v-row>
          <v-row>
            <v-spacer></v-spacer>
            <v-btn v-if="kontrak.is_amandemen == 0" elevation="0" dark color="light-blue" class="me-2 rounded-lg"
              @click="amandemen_dialog = true">Amandemen</v-btn>
            <v-btn v-if="kontrak.is_amandemen == 1" elevation="0" dark color="light-blue" class="rounded-lg"
              @click="updateData">Selesai Amandemen</v-btn>
          </v-row>
        </v-card-text>
      </v-card>

      <!-- dialog baru -->
      <v-dialog v-model="create_dialog" persistent max-width="650">
        <v-card class="rounded-lg">
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
                <v-btn dark text class="rounded-lg" @click="clearCreateData(0)">
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
                <v-btn dark text class="rounded-lg" @click="clearCreateData(1)">
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
                <v-btn dark class="rounded-lg" text @click="clearCreateData(2)">
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
                <v-btn dark text class="rounded-lg" @click="clearCreateData(3)">
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
                <v-btn dark text class="rounded-lg" @click="clearCreateData(4)">
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
            <v-text-field outlined hide-details="auto" dense label="Nama Jasa" :disabled="disabled"
              v-model="edit_data.nama_jasa" class="mb-3 rounded-lg"></v-text-field>
            <v-text-field outlined hide-details="auto" dense label="Harga" class="rounded-lg" :disabled="disabled"
              v-model="edit_data.harga"></v-text-field>
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
            <v-btn color="light" text class="rounded-lg" @click="closeDeleteDialog">
              Batal
            </v-btn>
            <v-btn color="red" dark elevation="0" class="rounded-lg" @click="deleteSave">
              Hapus
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </div>

    <!-- amandemen dialog -->
    <v-dialog v-model="amandemen_dialog" persistent max-width="400">
      <v-card class="rounded-lg">
        <v-card-title class="text-h5">
          Amandemen kontrak?
        </v-card-title>
        <v-card-text>
          <v-text-field outlined hide-details="auto" dense label="Nomor Amandemen" v-model="amandemen_data"
            class="mb-3 rounde-lg"></v-text-field>
          <small class="red--text">[!] Amandemen jasa tidak dapat dibatalkan</small>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="light" text class="rounded-lg" @click="closeAmandemenDialog">
            Batal
          </v-btn>
          <v-btn color="blue" dark elevation="0" class="rounded-lg" @click="saveAmandemen">
            Amandemen
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
  import Breadcrumbs from '~/components/header/Breadcrumbs.vue';
  import PageTitle from '~/components/header/PageTitle.vue';
  import KontrakNavbar from '~/components/KontrakNavbar.vue';

  const randomstring = require('randomstring')
  export default {
    components: {
      Breadcrumbs,
      PageTitle,
      KontrakNavbar,
    },

    data() {
      return {
        breadcrumbs_items: [],
        page_title: {},

        search: '',
        headers: [{
            text: 'Nama Jasa',
            align: 'start',
            value: 'nama_jasa',
          },
          {
            text: 'Nilai',
            value: 'harga',
          },
          {
            text: '',
            sortable: false,
            align: 'right',
            value: 'menu',
          },
        ],
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
        kontrak: {},
        amandemen_dialog: false,
        amandemen_data: ''
      }
    },
    created() {
      this.id = this.$route.params.id;
    },
    mounted() {
      this.getData();
      this.getKontrak();
      this.getWbsStats();
    },
    methods: {
      getData() {
        this.$axios.get(`/v1/kontrak/${this.id}/jasa`)
          .then(res => {
            this.items = res.data.data
          })
          .finally(() => {})
      },
      getKontrak() {
        this.$axios.get(`/v1/kontrak/${this.$route.params.id}`)
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
                text: 'Kontrak',
                disabled: false,
                href: '/tahapan/kontrak/1',
              },
              {
                text: res.data.data.nomor_kontrak,
                disabled: true,
                href: '/tahapan/kontrak/' + res.data.data.id,
              }
            ];

            this.page_title = {
              basket: res.data.data.basket,
              title: res.data.data.nomor_kontrak,
              description: res.data.data.pelaksana
            }

            this.kontrak = res.data.data
          })
      },
      getWbsStats() {
        this.$axios.get(`/v1/kontrak/${this.id}/wbs-jasa`)
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

        this.$axios.post(`/v1/kontrak/${this.$route.params.id}/jasa/${this.edit_data.id}`, data)
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
        this.$axios.delete(`/v1/kontrak/${this.$route.params.id}/jasa/${this.delete_data.id}`)
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
        this.$axios.post(`/v1/kontrak/${id}/jasa`, data)
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

      updateData() {
        let id = this.$route.params.id
        let data = {
          nomor_kontrak: this.kontrak.nomor_kontrak,
          nomor_po: this.kontrak.nomor_po,
          pelaksana: this.kontrak.pelaksana,
          direksi: this.kontrak.direksi,
          tanggal_kontrak: this.kontrak.tanggal_kontrak,
          tanggal_awal: this.kontrak.tanggal_awal,
          tanggal_akhir: this.kontrak.tanggal_akhir
        }
        this.disabled = true;
        this.$axios.post(`/v1/kontrak/${id}`, data)
          .then(res => {
            this.getData()
            this.getKontrak()
          })
          .catch(e => {
            alert(JSON.stringify(e.response))
          })
          .finally(() => {
            this.disabled = false
          })
      },
      saveAmandemen() {
        let data = {
          type: 'new',
          versi_amandemen: this.amandemen_data
        }

        this.$axios.post(`/v1/kontrak/${this.$route.params.id}/amandemen`, data)
          .then(res => {
            this.amandemen_data = '';
            this.amandemen_dialog = false;
            this.getData()
            this.getKontrak();
          })
      },
      closeAmandemenDialog() {
        this.amandemen_data = '';
        this.amandemen_dialog = false;
      },
      cancelAmandemen() {
        let data = {
          type: 'cancel'
        }

        this.$axios.post(`/v1/kontrak/${this.$route.params.id}/amandemen`, data)
          .then(res => {
            this.amandemen_data = '';
            this.amandemen_dialog = false;
            this.getData();
            this.getKontrak();
          })
      }
    },
    computed: {
      hasPermission() {
        return this.$auth.user.permissions.includes('edit-kontrak') || this.$auth.user.permissions.includes('superadmin');
      }
    }
  }
</script>

<style>

</style>