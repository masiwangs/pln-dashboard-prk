<template>
  <div class="container">
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

    <div class="d-flex justify-end">
      <v-breadcrumbs :items="breadcrumbItems"></v-breadcrumbs>
    </div>

    <div class="mb-5">
      <page-title title="Material" description="List database harga material" />
    </div>

    <div>
      <v-card elevation="4" class="rounded-lg">
        <v-card-title>
          <v-text-field class="rounded-lg" style="max-width: 300px" v-model="options.search" append-icon="mdi-magnify"
            label="Cari" single-line hide-details dense outlined></v-text-field>
          <v-spacer></v-spacer>
          <v-btn 
            v-if="hasPermission" 
            elevation="0" 
            class="rounded-lg me-2" 
            dark 
            color="green" 
            @click="import_dialog = true"
          >
            IMPORT
          </v-btn>
          <v-btn v-if="hasPermission" elevation="0" class="rounded-lg" dark color="light-blue" @click="create_dialog = true">
            BARU
          </v-btn>
        </v-card-title>
        <v-data-table 
          :headers="headers" 
          :items="materials" 
          :options.sync="options"
          :server-items-length="materials_count" 
          :loading="loading" 
          class="elevation-1"
        >
          <template v-slot:item.harga="{ item }">
            Rp{{ new Intl.NumberFormat('id-ID').format(item.harga) }}
          </template>
          <template v-slot:item.menu="{ item }">
            <div class="d-flex justify-end">
              <v-btn small dark color="amber" class="rounded-lg me-2" elevation="0" @click="openEditDialog(item.id)">
                <v-icon>mdi-pencil</v-icon>
              </v-btn>
              <v-btn small dark color="red" class="rounded-lg" elevation="0" @click="openDeleteDialog(item.id)">
                <v-icon>mdi-delete</v-icon>
              </v-btn>
            </div>
          </template>
        </v-data-table>
      </v-card>
    </div>

    <!-- dialog baru -->
    <v-dialog v-model="create_dialog" persistent max-width="1280">
      <v-card class="rounded-lg">
        <v-card-title class="text-h5">
          Tambah Material Baru
        </v-card-title>
        <v-card-text>
          <v-row>
            <v-col>
              <v-text-field outlined hide-details="auto" dense label="Kode Normalisasi" class="rounded-lg"
                v-model="create_data[0].kode_normalisasi"></v-text-field>
            </v-col>
            <v-col>
              <v-text-field outlined hide-details="auto" dense label="Nama Material" class="rounded-lg"
                v-model="create_data[0].nama_material">
              </v-text-field>
            </v-col>
            <v-col>
              <v-text-field outlined hide-details="auto" dense label="Harga" v-model="create_data[0].harga" class="rounded-lg"
                type="number">
              </v-text-field>
            </v-col>
            <v-col>
              <v-text-field outlined hide-details="auto" dense label="Satuan" v-model="create_data[0].satuan" class="rounded-lg">
              </v-text-field>
            </v-col>
            <v-col cols="auto">
              <v-btn dark text @click="clearCreateData(0)" class="rounded-lg">
                <v-icon color="red">mdi-delete</v-icon>
              </v-btn>
            </v-col>
          </v-row>
          <v-row>
            <v-col>
              <v-text-field outlined hide-details="auto" dense label="Kode Normalisasi" class="rounded-lg"
                v-model="create_data[1].kode_normalisasi"></v-text-field>
            </v-col>
            <v-col>
              <v-text-field outlined hide-details="auto" dense label="Nama Material" class="rounded-lg"
                v-model="create_data[1].nama_material">
              </v-text-field>
            </v-col>
            <v-col>
              <v-text-field outlined hide-details="auto" dense label="Harga" v-model="create_data[1].harga" class="rounded-lg"
                type="number">
              </v-text-field>
            </v-col>
            <v-col>
              <v-text-field outlined hide-details="auto" dense label="Satuan" v-model="create_data[1].satuan" class="rounded-lg">
              </v-text-field>
            </v-col>
            <v-col cols="auto">
              <v-btn dark text @click="clearCreateData(1)" class="rounded-lg">
                <v-icon color="red">mdi-delete</v-icon>
              </v-btn>
            </v-col>
          </v-row>
          <v-row>
            <v-col>
              <v-text-field outlined hide-details="auto" dense label="Kode Normalisasi" class="rounded-lg"
                v-model="create_data[2].kode_normalisasi"></v-text-field>
            </v-col>
            <v-col>
              <v-text-field outlined hide-details="auto" dense label="Nama Material" class="rounded-lg"
                v-model="create_data[2].nama_material">
              </v-text-field>
            </v-col>
            <v-col>
              <v-text-field outlined hide-details="auto" dense label="Harga" v-model="create_data[2].harga" class="rounded-lg"
                type="number">
              </v-text-field>
            </v-col>
            <v-col>
              <v-text-field outlined hide-details="auto" dense label="Satuan" v-model="create_data[2].satuan" class="rounded-lg">
              </v-text-field>
            </v-col>
            <v-col cols="auto">
              <v-btn dark text @click="clearCreateData(2)" class="rounded-lg">
                <v-icon color="red">mdi-delete</v-icon>
              </v-btn>
            </v-col>
          </v-row>
          <v-row>
            <v-col>
              <v-text-field outlined hide-details="auto" dense label="Kode Normalisasi" class="rounded-lg"
                v-model="create_data[3].kode_normalisasi"></v-text-field>
            </v-col>
            <v-col>
              <v-text-field outlined hide-details="auto" dense label="Nama Material" class="rounded-lg"
                v-model="create_data[3].nama_material">
              </v-text-field>
            </v-col>
            <v-col>
              <v-text-field outlined hide-details="auto" dense label="Harga" v-model="create_data[3].harga" class="rounded-lg"
                type="number">
              </v-text-field>
            </v-col>
            <v-col>
              <v-text-field outlined hide-details="auto" dense label="Satuan" v-model="create_data[3].satuan" class="rounded-lg">
              </v-text-field>
            </v-col>
            <v-col cols="auto">
              <v-btn dark text @click="clearCreateData(3)" class="rounded-lg">
                <v-icon color="red">mdi-delete</v-icon>
              </v-btn>
            </v-col>
          </v-row>
          <v-row>
            <v-col>
              <v-text-field outlined hide-details="auto" dense label="Kode Normalisasi" class="rounded-lg"
                v-model="create_data[4].kode_normalisasi"></v-text-field>
            </v-col>
            <v-col>
              <v-text-field outlined hide-details="auto" dense label="Nama Material" class="rounded-lg"
                v-model="create_data[4].nama_material">
              </v-text-field>
            </v-col>
            <v-col>
              <v-text-field outlined hide-details="auto" dense label="Harga" v-model="create_data[4].harga" class="rounded-lg"
                type="number">
              </v-text-field>
            </v-col>
            <v-col>
              <v-text-field outlined hide-details="auto" dense label="Satuan" v-model="create_data[4].satuan" class="rounded-lg">
              </v-text-field>
            </v-col>
            <v-col cols="auto">
              <v-btn dark text @click="clearCreateData(4)" class="rounded-lg">
                <v-icon color="red">mdi-delete</v-icon>
              </v-btn>
            </v-col>
          </v-row>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="light" class="rounded-lg" text @click="createCancel">
            Batal
          </v-btn>
          <v-btn color="light-blue" class="rounded-lg" dark elevation="0" @click="createSave">
            Simpan
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- edit dialog -->
    <v-dialog v-model="edit_dialog" persistent max-width="400">
      <v-card class="rounded-lg">
        <v-card-title class="text-h5">
          Ubah Material
        </v-card-title>
        <v-card-text>
          <table class="mb-3">
            <tbody>
              <tr>
                <th class="text-left">Normalisasi</th>
                <td>{{ edit_data.kode_normalisasi }}</td>
              </tr>
              <tr>
                <th class="text-left">Nama Material</th>
                <td>{{ edit_data.nama_material }}</td>
              </tr>
              <tr>
                <th class="text-left">Satuan</th>
                <td>{{ edit_data.satuan }}</td>
              </tr>
            </tbody>
          </table>
          <v-text-field outlined hide-details="auto" dense class="rounded-lg" label="Harga" :disabled="disabled" prefix="Rp" type="number"
            v-model="edit_data.harga">
          </v-text-field>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="light" class="rounded-lg" text :disabled="disabled" @click="closeEditDialog">
            Batal
          </v-btn>
          <v-btn color="blue" class="rounded-lg" dark elevation="0" :disabled="disabled" @click="updateSave">
            SIMPAN
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- dialog delete -->
    <v-dialog v-model="delete_dialog" persistent max-width="400">
      <v-card class="rounded-lg">
        <v-card-title class="text-h5">
          Hapus Material?
        </v-card-title>
        <v-card-text>
          Apakah Anda yakin menghapus material ini? <br />
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

    <import-dialog 
      template-url="http://monitoring-api.test/templates/import_material_template.xlsx"
      upload-url="/v1/material/import"
      dialog-title="Import Material"
      :dialog="import_dialog"
      @done="doneImport()" 
      @error="errorImport"
      @cancel="import_dialog = false" 
    />
  </div>
</template>
<script>
  import PageTitle from '~/components/PageTitle.vue';
  import BasketTab from '~/components/BasketTab.vue';
  import ImportDialog from '~/components/dialog/ImportDialog.vue';
  export default {
    components: {
      PageTitle,
      BasketTab,
      ImportDialog
    },
    data() {
      return {
        breadcrumbItems: [{
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
            disabled: true,
            href: '/tahapan/kontrak',
          },
        ],

        search: '',
        loading: false,
        disabled: false,
        materials: [],
        materials_count: 0,
        options: {},
        
        options: {},
        edit_dialog: false,
        edit_data: {
          id: '',
          kode_normalisasi: '',
          satuan: '',
          harga: ''
        },
        create_data: [{
            kode_normalisasi: '',
            nama_material: '',
            satuan: '',
            harga: ''
          },
          {
            kode_normalisasi: '',
            nama_material: '',
            satuan: '',
            harga: ''
          },
          {
            kode_normalisasi: '',
            nama_material: '',
            satuan: '',
            harga: ''
          },
          {
            kode_normalisasi: '',
            nama_material: '',
            satuan: '',
            harga: ''
          },
          {
            kode_normalisasi: '',
            nama_material: '',
            satuan: '',
            harga: ''
          },
        ],
        create_dialog: false,
        delete_dialog: false,
        delete_data: '',

        import_dialog: false,

        snackbar: false,
        snackbar_text: ''
      }
    },
    watch: {
      options: {
        handler() {
          this.getDataFromApi()
        },
        deep: true,
      },
    },
    methods: {
      getDataFromApi() {
        this.loading = true
        this.$axios.get(
            `/v1/material?search=${this.options.search ? this.options.search : ''}&sortby=${this.options.sortBy}&sortdesc=${this.options.sortDesc}&page=${this.options.page}&perpage=${this.options.itemsPerPage}`
            )
          .then(res => {
            this.materials = res.data.data
            this.materials_count = res.data.count
            this.loading = false
          })
      },
      openEditDialog(id) {
        const material = this.materials.find(x => x.id == id)
        this.edit_data = material;
        this.edit_dialog = true
      },
      closeEditDialog() {
        this.edit_data = {
          id: '',
          kode_normalisasi: '',
          satuan: '',
          harga: ''
        }
        this.edit_dialog = false
      },
      updateSave() {
        let id = this.edit_data.id;
        let data = {
          'harga': this.edit_data.harga
        };

        this.$axios.post(`/v1/material/${id}`, data)
          .then(res => {
            this.getDataFromApi()
            this.closeEditDialog()
          })
      },
      clearCreateData(index) {
        this.create_data[index].kode_normalisasi = '';
        this.create_data[index].nama_material = '';
        this.create_data[index].satuan = '';
        this.create_data[index].harga = '';
      },
      createCancel() {
        this.create_data = [{
            kode_normalisasi: '',
            nama_material: '',
            satuan: '',
            harga: ''
          },
          {
            kode_normalisasi: '',
            nama_material: '',
            satuan: '',
            harga: ''
          },
          {
            kode_normalisasi: '',
            nama_material: '',
            satuan: '',
            harga: ''
          },
          {
            kode_normalisasi: '',
            nama_material: '',
            satuan: '',
            harga: ''
          },
          {
            kode_normalisasi: '',
            nama_material: '',
            satuan: '',
            harga: ''
          }
        ];

        this.create_dialog = false;
      },
      createSave() {
        let data = this.create_data;

        this.$axios.post(`/v1/material`, data)
          .then(res => {
            this.getDataFromApi()
            this.createCancel()
          })
      },
      closeDeleteDialog() {
        this.delete_data = '';
        this.delete_dialog = false
      },
      deleteSave() {
        let id = this.delete_data;
        this.$axios.delete(`/v1/material/${id}`)
          .then(res => {
            this.getDataFromApi();
            this.delete_data = '';
            this.delete_dialog = false;
          })
      },
      openDeleteDialog(id) {
        this.delete_data = id;
        this.delete_dialog = true;
      },
      doneImport() {
        this.import_dialog = false;
        this.snackbar_text = 'Import material berhasil.';
        this.snackbar = true;
        this.getDataFromApi();
      },
      errorImport(messages) {
        this.snackbar_text = messages[0];
        this.snackbar = true;
        this.getDataFromApi();
      }
    },
    computed: {
      hasPermission() {
        return this.$auth.user.permissions.includes('edit-material') || this.$auth.user.permissions.includes('superadmin');
      },
      headers() {
        let headers = [{
            text: 'Kode Normalisasi',
            align: 'start',
            value: 'kode_normalisasi',
          },
          {
            text: 'Nama Material',
            value: 'nama_material',
          },
          {
            text: 'Satuan',
            value: 'satuan',
          },
          {
            text: 'Harga',
            value: 'harga',
          }
        ];

        if(this.hasPermission) {
          headers.push({
            text: '',
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