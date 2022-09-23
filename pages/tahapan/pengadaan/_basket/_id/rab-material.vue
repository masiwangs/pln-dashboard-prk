<template>
  <div>
    <breadcrumbs :items="breadcrumbs_items" />
    <page-title :item="page_title" />

    <div class="mb-5">
      <pengadaan-navbar active="rab-material" :has-permission="hasPermission"/>

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
          <template v-slot:item.nomor_prk="{ item }">
            <nuxt-link :to="'/tahapan/prk/'+item.prk.basket+'/'+item.prk_id">
              {{ item.prk.nomor_prk }}
            </nuxt-link>
          </template>
          <template v-slot:item.harga="{ item }">
            Rp{{ new Intl.NumberFormat('id-ID').format(item.harga) }}
          </template>
          <template v-slot:item.harga="{ item }">
            Rp{{ new Intl.NumberFormat('id-ID').format(item.harga) }}
          </template>
          <template v-slot:item.sub_total="{ item }">
            Rp{{ new Intl.NumberFormat('id-ID').format(item.harga * item.jumlah) }}
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
      <v-dialog v-model="create_dialog" persistent max-width="1366">
        <v-card class="rounded-lg">
          <v-card-title class="text-h5">
            Tambah Material Baru
          </v-card-title>
          <v-card-text>
            <v-row>
              <v-col cols="12" sm="auto" lg="5">
                <v-autocomplete outlined hide-details="auto" dense label="Nama Material" v-model="create_data[0].id"
                  :items="material_options" item-text="nama_material" item-value="id" @change="getMaterialDetail(0)"
                  class="rounded-lg">
                  <template v-slot:item="data">
                    <template>
                      <v-list-item-content>
                        <v-list-item-title v-html="'No. PRK: '+data.item.prk.nomor_prk"></v-list-item-title>
                        <v-list-item-subtitle v-html="data.item.nama_material"></v-list-item-subtitle>
                        <v-list-item-subtitle v-html="'Jumlah: '+data.item.jumlah"></v-list-item-subtitle>
                      </v-list-item-content>
                    </template>
                  </template>
                </v-autocomplete>
              </v-col>
              <v-col cols="12" sm="auto" lg="2">
                <v-text-field outlined hide-details="auto" dense label="Harga" prefix="Rp" type="number"
                  v-model="create_data[0].harga" class="rounded-lg" readonly @change="getMaterialDetail(0)">
                </v-text-field>
              </v-col>
              <v-col cols="12" sm="auto" lg="2">
                <v-text-field outlined hide-details="auto" dense label="Satuan" v-model="create_data[0].satuan"
                  class="rounded-lg" readonly></v-text-field>
              </v-col>
              <v-col cols="12" sm="auto" lg="2">
                <v-text-field outlined hide-details="auto" dense label="Jumlah" v-model="create_data[0].jumlah"
                  class="rounded-lg" type="number"></v-text-field>
              </v-col>
              <v-col cols="auto" class="d-none d-sm-block">
                <v-btn dark text @click="clearCreateData(0)">
                  <v-icon color="red">mdi-delete</v-icon>
                </v-btn>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12" sm="auto" lg="5">
                <v-autocomplete outlined hide-details="auto" dense label="Nama Material" v-model="create_data[1].id"
                  class="rounded-lg" :items="material_options" item-text="nama_material" item-value="id"
                  @change="getMaterialDetail(1)">
                  <template v-slot:item="data">
                    <template>
                      <v-list-item-content>
                        <v-list-item-title v-html="'No. PRK: '+data.item.prk.nomor_prk"></v-list-item-title>
                        <v-list-item-subtitle v-html="data.item.nama_material"></v-list-item-subtitle>
                        <v-list-item-subtitle v-html="'Jumlah: '+data.item.jumlah"></v-list-item-subtitle>
                      </v-list-item-content>
                    </template>
                  </template>
                </v-autocomplete>
              </v-col>
              <v-col cols="12" sm="auto" lg="2">
                <v-text-field outlined hide-details="auto" dense label="Harga" prefix="Rp" type="number"
                  v-model="create_data[1].harga" class="rounded-lg" readonly></v-text-field>
              </v-col>
              <v-col cols="12" sm="auto" lg="2">
                <v-text-field outlined hide-details="auto" dense label="Satuan" v-model="create_data[1].satuan"
                  class="rounded-lg" readonly></v-text-field>
              </v-col>
              <v-col cols="12" sm="auto" lg="2">
                <v-text-field outlined hide-details="auto" dense label="Jumlah" v-model="create_data[1].jumlah"
                  class="rounded-lg" type="number"></v-text-field>
              </v-col>
              <v-col cols="auto" class="d-none d-sm-block">
                <v-btn dark text @click="clearCreateData(1)" class="rounded-lg">
                  <v-icon color="red">mdi-delete</v-icon>
                </v-btn>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12" sm="auto" lg="5">
                <v-autocomplete outlined hide-details="auto" dense label="Nama Material" v-model="create_data[2].id"
                  :items="material_options" item-text="nama_material" item-value="id" @change="getMaterialDetail(2)"
                  class="rounded-lg">
                  <template v-slot:item="data">
                    <template>
                      <v-list-item-content>
                        <v-list-item-title v-html="'No. PRK: '+data.item.prk.nomor_prk"></v-list-item-title>
                        <v-list-item-subtitle v-html="data.item.nama_material"></v-list-item-subtitle>
                        <v-list-item-subtitle v-html="'Jumlah: '+data.item.jumlah"></v-list-item-subtitle>
                      </v-list-item-content>
                    </template>
                  </template>
                </v-autocomplete>
              </v-col>
              <v-col cols="12" sm="auto" lg="2">
                <v-text-field outlined hide-details="auto" dense label="Harga" prefix="Rp" type="number"
                  v-model="create_data[2].harga" class="rounded-lg" readonly></v-text-field>
              </v-col>
              <v-col cols="12" sm="auto" lg="2">
                <v-text-field outlined hide-details="auto" dense label="Satuan" v-model="create_data[2].satuan"
                  class="rounded-lg" readonly></v-text-field>
              </v-col>
              <v-col cols="12" sm="auto" lg="2">
                <v-text-field outlined hide-details="auto" dense label="Jumlah" v-model="create_data[2].jumlah"
                  class="rounded-lg" type="number"></v-text-field>
              </v-col>
              <v-col cols="auto" class="d-none d-sm-block">
                <v-btn dark text @click="clearCreateData(2)" class="rounded-lg">
                  <v-icon color="red">mdi-delete</v-icon>
                </v-btn>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12" sm="auto" lg="5">
                <v-autocomplete outlined hide-details="auto" dense label="Nama Material" v-model="create_data[3].id"
                  :items="material_options" :item-text="'NO. PRK: '+prk_id+'<br/>'+nama_material" item-value="id"
                  @change="getMaterialDetail(3)" class="rounded-lg">
                  <template v-slot:item="data">
                    <template>
                      <v-list-item-content>
                        <v-list-item-title v-html="'No. PRK: '+data.item.prk.nomor_prk"></v-list-item-title>
                        <v-list-item-subtitle v-html="data.item.nama_material"></v-list-item-subtitle>
                        <v-list-item-subtitle v-html="'Jumlah: '+data.item.jumlah"></v-list-item-subtitle>
                      </v-list-item-content>
                    </template>
                  </template>
                </v-autocomplete>
              </v-col>
              <v-col cols="12" sm="auto" lg="2">
                <v-text-field outlined hide-details="auto" dense label="Harga" prefix="Rp" type="number"
                  v-model="create_data[3].harga" class="rounded-lg" readonly></v-text-field>
              </v-col>
              <v-col cols="12" sm="auto" lg="2">
                <v-text-field outlined hide-details="auto" dense label="Satuan" v-model="create_data[3].satuan"
                  class="rounded-lg" readonly></v-text-field>
              </v-col>
              <v-col cols="12" sm="auto" lg="2">
                <v-text-field outlined hide-details="auto" dense label="Jumlah" v-model="create_data[3].jumlah"
                  class="rounded-lg" type="number"></v-text-field>
              </v-col>
              <v-col cols="auto" class="d-none d-sm-block">
                <v-btn dark text @click="clearCreateData(3)" class="rounded-lg">
                  <v-icon color="red">mdi-delete</v-icon>
                </v-btn>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12" sm="auto" lg="5">
                <v-autocomplete outlined hide-details="auto" dense label="Nama Material" v-model="create_data[4].id"
                  :items="material_options" item-text="nama_material" item-value="id" @change="getMaterialDetail(4)"
                  class="rounded-lg">
                  <template v-slot:item="data">
                    <template>
                      <v-list-item-content>
                        <v-list-item-title v-html="'No. PRK: '+data.item.prk.nomor_prk"></v-list-item-title>
                        <v-list-item-subtitle v-html="data.item.nama_material"></v-list-item-subtitle>
                        <v-list-item-subtitle v-html="'Jumlah: '+data.item.jumlah"></v-list-item-subtitle>
                      </v-list-item-content>
                    </template>
                  </template>
                </v-autocomplete>
              </v-col>
              <v-col cols="12" sm="auto" lg="2">
                <v-text-field outlined hide-details="auto" dense label="Harga" prefix="Rp" type="number"
                  v-model="create_data[4].harga" class="rounded-lg" readonly></v-text-field>
              </v-col>
              <v-col cols="12" sm="auto" lg="2">
                <v-text-field outlined hide-details="auto" dense label="Satuan" v-model="create_data[4].satuan"
                  class="rounded-lg" readonly></v-text-field>
              </v-col>
              <v-col cols="12" sm="auto" lg="2">
                <v-text-field outlined hide-details="auto" dense label="Jumlah" v-model="create_data[4].jumlah"
                  class="rounded-lg" type="number"></v-text-field>
              </v-col>
              <v-col cols="auto" class="d-none d-sm-block">
                <v-btn dark text @click="clearCreateData(4)" class="rounded-lg">
                  <v-icon color="red">mdi-delete</v-icon>
                </v-btn>
              </v-col>
            </v-row>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="light" text class="rounded-lg" @click="closeCreateDialog">
              Batal
            </v-btn>
            <v-btn color="blue" dark elevation="0" class="rounded-lg" @click="createSave">
              Simpan
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <!-- dialog edit -->
      <v-dialog v-model="edit_dialog" persistent max-width="400">
        <v-card class="rounded-lg">
          <v-card-title class="text-h5">
            Ubah Material
          </v-card-title>
          <v-card-text>
            <v-text-field outlined hide-details="auto" dense label="Nama Material" :disabled="disabled"
              v-model="edit_data.nama_material" readonly class="mb-3 rounded-lg"></v-text-field>
            <v-text-field outlined hide-details="auto" dense label="Harga" :disabled="disabled"
              v-model="edit_data.harga" readonly class="mb-3 rounded-lg"></v-text-field>
            <v-text-field outlined hide-details="auto" dense label="Satuan" :disabled="disabled"
              v-model="edit_data.satuan" readonly class="mb-3 rounded-lg"></v-text-field>
            <v-text-field outlined hide-details="auto" dense label="Harga" :disabled="disabled"
              v-model="edit_data.harga" readonly class="mb-3 rounded-lg"></v-text-field>
            <v-text-field outlined hide-details="auto" dense label="Jumlah" :disabled="disabled"
              v-model="edit_data.jumlah" class="rounded-lg"></v-text-field>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="light" text :disabled="disabled" @click="closeEditDialog" class="rounded-lg">
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
            <v-btn color="light" text @click="closeDeleteDialog" class="rounded-lg">
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
  import SkkiNavbar from '~/components/SkkiNavbar.vue';

  const randomstring = require('randomstring')
  export default {
    components: {
      Breadcrumbs,
      PageTitle,
      SkkiNavbar,
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
        edit_data: {},
        disabled: false,
        table_key: '1',
        delete_data: '',
        material_options: [],
        wbs_stats: {
          total: 0,
          used: 0,
          rest: 0
        },
        create_data: [{
            id: '',
            kode_normalisasi: '',
            nama_material: '',
            satuan: '',
            harga: '',
            jumlah: ''
          },
          {
            id: '',
            kode_normalisasi: '',
            nama_material: '',
            satuan: '',
            harga: '',
            jumlah: ''
          },
          {
            id: '',
            kode_normalisasi: '',
            nama_material: '',
            satuan: '',
            harga: '',
            jumlah: ''
          },
          {
            id: '',
            kode_normalisasi: '',
            nama_material: '',
            satuan: '',
            harga: '',
            jumlah: ''
          },
          {
            id: '',
            kode_normalisasi: '',
            nama_material: '',
            satuan: '',
            harga: '',
            jumlah: ''
          },
        ],
      }
    },
    created() {
      this.id = this.$route.params.id;
    },
    mounted() {
      this.getData()
      this.getWbsStats()
      this.getMaterialOptions()
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
        this.$axios.get(`/v1/pengadaan/${this.id}/material`)
          .then(res => {
            this.items = res.data.data
          })
          .finally(() => {})
      },
      getMaterialOptions() {
        this.$axios.get(`/v1/pengadaan/${this.id}/stok-material`)
          .then(res => {
            this.material_options = res.data.data
          })
      },
      getWbsStats() {
        this.$axios.get(`/v1/pengadaan/${this.id}/wbs-material`)
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
        const material = this.items.find(x => x.id == item_id)
        this.edit_data = material
        this.edit_dialog = true
      },
      closeEditDialog() {
        this.edit_data = {}
        this.edit_dialog = false
      },
      updateSave() {
        let prk_id = this.$route.params.id
        let material_id = this.edit_data.id

        let data = {
          jumlah: this.edit_data.jumlah
        }

        this.disabled = true;

        this.$axios.post(`/v1/pengadaan/${this.edit_data.pengadaan_id}/material/${this.edit_data.id}`, data)
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
        this.$axios.delete(`/v1/pengadaan/${this.delete_data.pengadaan_id}/material/${this.delete_data.id}`)
          .then(res => {
            this.getData()
            this.closeDeleteDialog()
            this.getWbsStats()
          })
          .finally(() => {
            this.disabled = false
          })
      },
      getMaterialDetail(index) {
        const material = this.material_options.find(x => x.id == this.create_data[index].id)
        this.create_data[index].satuan = material.satuan;
        this.create_data[index].harga = material.harga;
        this.create_data[index].jumlah = material.jumlah;
      },
      closeCreateDialog() {
        this.create_data = [{
            id: '',
            kode_normalisasi: '',
            nama_material: '',
            satuan: '',
            harga: '',
            jumlah: ''
          },
          {
            id: '',
            kode_normalisasi: '',
            nama_material: '',
            satuan: '',
            harga: '',
            jumlah: ''
          },
          {
            id: '',
            kode_normalisasi: '',
            nama_material: '',
            satuan: '',
            harga: '',
            jumlah: ''
          },
          {
            id: '',
            kode_normalisasi: '',
            nama_material: '',
            satuan: '',
            harga: '',
            jumlah: ''
          },
          {
            id: '',
            kode_normalisasi: '',
            nama_material: '',
            satuan: '',
            harga: '',
            jumlah: ''
          },
        ];

        this.create_dialog = false
      },
      createSave() {
        let id = this.$route.params.id
        let data = this.create_data;
        this.disabled = true;
        this.$axios.post(`/v1/pengadaan/${id}/material`, data)
          .then(res => {
            this.getData()
            this.closeCreateDialog()
            this.getWbsStats()
          })
          .finally(() => {
            this.disabled = false;
          })
      },
      clearCreateData(row) {
        this.create_data[row].id = '';
        this.create_data[row].nama_material = '';
        this.create_data[row].satuan = '';
        this.create_data[row].harga = '';
        this.create_data[row].jumlah = '';
      },
    },
    computed: {
      hasPermission() {
        return this.$auth.user.permissions.includes('edit-pengadaan') || this.$auth.user.permissions.includes('superadmin');
      },
      headers() {
        let headers = [{
            text: 'Nomor PRK',
            align: 'start',
            value: 'nomor_prk',
          },
          {
            text: 'Normalisasi',
            value: 'kode_normalisasi',
          },
          {
            text: 'Nama Material',
            value: 'nama_material',
          },
          {
            text: 'Harga',
            value: 'harga',
          },
          {
            text: 'Jumlah',
            value: 'jumlah',
          },
          {
            text: 'Sub Total',
            value: 'sub_total',
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