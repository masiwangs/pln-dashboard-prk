<template>
  <div>
    <breadcrumbs :items="breadcrumbs_items" />
    <page-title :item="page_title" />

    <div class="mb-5">
      <prk-navbar active="rab-jasa" :has-permission="hasPermission"/>

      <v-card elevation="4" class="rounded-lg">
        <v-card-title>
          <v-text-field style="max-width: 300px" v-model="search" append-icon="mdi-magnify" label="Cari" single-line
            hide-details="auto" dense outlined></v-text-field>
          <v-spacer></v-spacer>
          <v-btn v-if="hasPermission" dark color="blue" elevation="0" @click="create_dialog = true" class="rounded-lg">Baru</v-btn>
        </v-card-title>
        <v-data-table :headers="headers" :items="items" :items-per-page="5" class="elevation-0">
          <template v-slot:item.harga="{ item }">
            Rp{{ new Intl.NumberFormat('id-ID').format(item.harga) }}
          </template>
          <template v-slot:item.menu="{ item }">
            <div class="d-flex justify-end">
              <v-btn small dark color="amber" elevation="0" @click="openEditDialog(item.id)" class="rounded-lg me-2">
                <v-icon>mdi-pencil</v-icon>
              </v-btn>
              <v-btn small dark color="red" elevation="0" @click="openDeleteDialog(item.id)" class="rounded-lg">
                <v-icon>mdi-delete</v-icon>
              </v-btn>
            </div>
          </template>
        </v-data-table>
      </v-card>

      <!-- dialog baru -->
      <v-dialog v-model="create_dialog" persistent max-width="630">
        <v-card>
          <v-card-title class="text-h5">
            Tambah Jasa Baru
          </v-card-title>
          <v-card-text>
            <v-row>
              <v-col cols="12" sm="auto">
                <v-text-field outlined hide-details="auto" dense label="Nama Jasa" v-model="create_data[0].nama_jasa"
                  class="rounded-lg"></v-text-field>
              </v-col>
              <v-col cols="12" sm="auto">
                <v-text-field outlined hide-details="auto" dense label="Harga" prefix="Rp"
                  v-model="create_data[0].harga" class="rounded-lg" type="number"></v-text-field>
              </v-col>
              <v-col cols="auto" class="d-none d-sm-block">
                <v-btn dark text @click="clearCreateData(0)">
                  <v-icon color="red">mdi-delete</v-icon>
                </v-btn>
              </v-col>
            </v-row>
            <v-row class="d-none d-sm-flex">
              <v-col cols="12" sm="auto">
                <v-text-field outlined hide-details="auto" dense label="Nama Jasa" v-model="create_data[1].nama_jasa"
                  class="rounded-lg"></v-text-field>
              </v-col>
              <v-col cols="12" sm="auto">
                <v-text-field outlined hide-details="auto" dense label="Harga" prefix="Rp"
                  v-model="create_data[1].harga" class="rounded-lg" type="number"></v-text-field>
              </v-col>
              <v-col cols="auto">
                <v-btn dark text @click="clearCreateData(1)">
                  <v-icon color="red">mdi-delete</v-icon>
                </v-btn>
              </v-col>
            </v-row>
            <v-row class="d-none d-sm-flex">
              <v-col cols="12" sm="auto">
                <v-text-field outlined hide-details="auto" dense label="Nama Jasa" v-model="create_data[2].nama_jasa"
                  class="rounded-lg"></v-text-field>
              </v-col>
              <v-col cols="12" sm="auto">
                <v-text-field outlined hide-details="auto" dense label="Harga" prefix="Rp"
                  v-model="create_data[2].harga" class="rounded-lg" type="number"></v-text-field>
              </v-col>
              <v-col cols="auto">
                <v-btn dark text @click="clearCreateData(2)">
                  <v-icon color="red">mdi-delete</v-icon>
                </v-btn>
              </v-col>
            </v-row>
            <v-row class="d-none d-sm-flex">
              <v-col cols="12" sm="auto">
                <v-text-field outlined hide-details="auto" dense label="Nama Jasa" v-model="create_data[3].nama_jasa"
                  class="rounded-lg"></v-text-field>
              </v-col>
              <v-col cols="12" sm="auto">
                <v-text-field outlined hide-details="auto" dense label="Harga" prefix="Rp"
                  v-model="create_data[3].harga" class="rounded-lg" type="number"></v-text-field>
              </v-col>
              <v-col cols="auto">
                <v-btn dark text @click="clearCreateData(3)">
                  <v-icon color="red">mdi-delete</v-icon>
                </v-btn>
              </v-col>
            </v-row>
            <v-row class="d-none d-sm-flex">
              <v-col cols="12" sm="auto">
                <v-text-field outlined hide-details="auto" dense label="Nama Jasa" v-model="create_data[4].nama_jasa"
                  class="rounded-lg"></v-text-field>
              </v-col>
              <v-col cols="12" sm="auto">
                <v-text-field outlined hide-details="auto" dense label="Harga" prefix="Rp"
                  v-model="create_data[4].harga" class="rounded-lg" type="number"></v-text-field>
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
            <v-btn color="light" text @click="createCancel" class="rounded-lg">
              Batal
            </v-btn>
            <v-btn color="blue" dark elevation="0" @click="createSave" class="rounded-lg">
              Simpan
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <!-- dialog edit -->
      <v-dialog v-model="edit_dialog" persistent max-width="400">
        <v-card>
          <v-card-title class="text-h5">
            Ubah Jasa
          </v-card-title>
          <v-card-text>
            <v-text-field outlined hide-details="auto" dense label="Nama Jasa" :disabled="disabled"
              v-model="edit_data.nama_jasa" class="mb-3"></v-text-field>
            <v-text-field outlined hide-details="auto" dense label="Harga" :disabled="disabled"
              v-model="edit_data.harga"></v-text-field>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="light" text :disabled="disabled" @click="closeEditDialog">
              Batal
            </v-btn>
            <v-btn color="blue" text :disabled="disabled" @click="updateSave">
              SIMPAN
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <!-- dialog delete -->
      <v-dialog v-model="delete_dialog" persistent max-width="400">
        <v-card>
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
            <v-btn color="red" text @click="deleteSave">
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

        search: '',
        items: [],
        create_dialog: false,
        edit_dialog: false,
        delete_dialog: false,
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
        edit_data: {
          id: '',
          nama_jasa: '',
          harga_jasa: ''
        },
        disabled: false,
        table_key: '1',
        delete_data: '',
      }
    },
    created() {
      this.id = this.$route.params.id;
    },
    mounted() {
      this.$axios.get(`/v1/prk/${this.$route.params.id}`)
        .then(res => {
          this.breadcrumbs_items = [{
              text: 'Dashboard',
              disabled: false,
              href: '/',
            },
            {
              text: 'Tahapan',
              disabled: false,
              href: '/',
            },
            {
              text: 'PRK',
              disabled: false,
              href: '/tahapan/prk/'+this.$route.params.basket,
            },
            {
              text: res.data.data.nomor_prk,
              disabled: true,
              href: '/tahapan/prk/'+this.$route.params.basket+'/'+this.$route.params.id,
            }
          ];
          this.page_title = {
            basket: res.data.data.basket,
            title: res.data.data.nomor_prk,
            description: res.data.data.nama_project
          }
        })

      this.getData();
    },
    methods: {
      getData() {
        this.$axios.get(`/v1/prk/${this.id}/jasa`)
          .then(res => {
            this.items = res.data.data
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
        let data = this.create_data;
        this.disabled = true;
        this.$axios.post(`/v1/prk/${id}/jasa`, data)
          .then(res => {
            this.getData()
            this.closeCreateDialog()
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
      createCancel() {
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
        this.create_dialog = false;
      },
      clearCreateData(row) {
        this.create_data[row].nama_jasa = '';
        this.create_data[row].harga = '';
      },
      openEditDialog(item_id) {
        const jasa = this.items.find(x => x.id == item_id)
        this.edit_data = {
          id: jasa.id,
          nama_jasa: jasa.nama_jasa,
          harga: jasa.harga
        }
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
        let prk_id = this.$route.params.id
        let jasa_id = this.edit_data.id

        let data = {
          nama_jasa: this.edit_data.nama_jasa,
          harga: this.edit_data.harga
        }

        this.disabled = true;

        this.$axios.post(`/v1/prk/${prk_id}/jasa/${jasa_id}`, data)
          .then(res => {
            this.getData()
            this.closeEditDialog()
          })
          .finally(() => {
            this.disabled = false;
          })
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
        let prk_id = this.$route.params.id;
        let jasa_id = this.delete_data;

        this.disabled = true;
        this.$axios.delete(`/v1/prk/${prk_id}/jasa/${jasa_id}`)
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
        return this.$auth.user.permissions.includes('edit-prk') || this.$auth.user.permissions.includes('superadmin');
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
          })
        }

        return headers;
      }
    }
  }
</script>

<style>

</style>