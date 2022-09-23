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
    <page-title title="PRK" description="List project dalam tahap PRK" />
  </div>

  <v-card
    elevation="4"
    class="rounded-lg mb-5"
  >
    <v-tabs 
      hide-slider 
      color="yellow"
      background-color="light-blue"
      dark
    >
      <v-tab nuxt exact :key="1" to="/tahapan/prk/1">BASKET 1</v-tab>
      <v-tab nuxt exact :key="2" to="/tahapan/prk/2">BASKET 2</v-tab>
      <v-tab nuxt exact :key="3" to="/tahapan/prk/3">BASKET 3</v-tab>
    </v-tabs>
  </v-card>
  
  <v-card
    elevation="4"
    class="rounded-lg"
  >
    <v-card-title>
      <v-text-field 
        style="max-width: 300px" 
        v-model="search" 
        append-icon="mdi-magnify" 
        label="Cari" 
        single-line
        hide-details="auto"
        class="rounded-lg"
        outlined
        dense
        ></v-text-field>
      <v-spacer></v-spacer>
      <v-btn 
        v-show="$auth.user.permissions.includes('edit-prk') || $auth.user.permissions.includes('superadmin')"
        color="green" 
        dark 
        elevation="0" 
        @click="import_dialog = true"
        class="rounded-lg me-2"
        >IMPORT</v-btn>
      <v-btn 
        v-show="$auth.user.permissions.includes('edit-prk') || $auth.user.permissions.includes('superadmin')"
        color="blue" 
        dark 
        elevation="0" 
        @click="create_dialog = true"
        class="rounded-lg"
        >BARU</v-btn>
    </v-card-title>
    <v-data-table :headers="headers" :items="prks" :loading="loading" class="elevation-1">
      <template v-slot:item.nama_project="{ item }">
        <nuxt-link :to="'/tahapan/prk/'+basket_no+'/'+item.id">
          {{ item.nama_project }}
        </nuxt-link>
      </template>
      <template v-slot:item.type="{ item }">
        {{ item.type == 1 ? 'MURNI' : 'LUNCURAN' }}
      </template>
      <template v-slot:item.rab_j="{ item }">
        Rp{{ item.rab_jasa ? new Intl.NumberFormat('id-ID').format(item.rab_jasa.rab_jasa) : 0 }}
      </template>
      <template v-slot:item.rab_m="{ item }">
        Rp{{ item.rab_material ? new Intl.NumberFormat('id-ID').format(item.rab_material.rab_material) : 0 }}
      </template>
    </v-data-table>
  </v-card>
  
  <v-dialog
      v-model="create_dialog"
      persistent
      max-width="400"
    >
      <v-card
        class="rounded-lg"
      >
        <v-card-title class="text-h5">
          Tambah PRK Baru
        </v-card-title>
        <v-card-text>
          <v-text-field
              outlined
              hide-details="auto"
              dense
              label="Nomor PRK"
              class="mb-3 rounded-lg"
              v-model="create_data.nomor_prk"
              :error="create_data_error.nomor_prk"
              :error-messages="create_data_error.nomor_prk"
          ></v-text-field>
          <v-text-field
              outlined
              hide-details="auto"
              dense
              label="Nama Project"
              class="mb-3 rounded-lg"
              v-model="create_data.nama_project"
          ></v-text-field>
          <v-text-field
              outlined
              hide-details="auto"
              dense
              label="Nomor Lot"
              class="mb-3 rounded-lg"
              v-model="create_data.nomor_lot"
              type="number"
          ></v-text-field>
          <v-select
            :items="prioritas_options"
            label="Prioritas"
            outlined
            v-model="create_data.prioritas"
            dense
            hide-details="auto"
            type="number"
            class="rounded-lg mb-3"
          ></v-select>
          <v-select 
            :items="[{value: 1, text: 'MURNI'}, {value: 2, text: 'LUNCURAN'}]" 
            label="Tipe" 
            hide-details="auto" 
            dense 
            v-model="create_data.type"
            class="rounded-lg"
            outlined></v-select>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="light"
            text
            @click="createCancel"
            class="rounded-lg"
          >
            Batal
          </v-btn>
          <v-btn
            color="blue"
            dark
            elevation="0"
            class="rounded-lg"
            @click="createSave"
          >
            Simpan
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <import-dialog 
      template-url="https://api-monitoring.masiwang.my.id/templates/import_prk_template.xlsx"
      upload-url="/v1/prk/import"
      dialog-title="Import PRK"
      :dialog="import_dialog"
      @done="doneImport" 
      @error="errorImport"
      @cancel="import_dialog = false" 
    />
</div>
</template>
<script>
import ImportDialog from '~/components/dialog/ImportDialog.vue'
import PageTitle from '~/components/PageTitle.vue'
export default {
  components: { PageTitle, ImportDialog },

  data() {
    return {
      breadcrumbItems: [
        {
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
          disabled: true,
          href: '/tahapan/prk/1',
        },
      ],

      loading: false,
      basket_no: 1,
      prks: [],
      search: '',
      options: {},
      headers: [
        {
          text: 'Nama Project',
          align: 'start',
          value: 'nama_project',
        },
        {
          text: 'Nomor PRK',
          value: 'nomor_prk',
        },
        {
          text: 'Tipe',
          value: 'type',
        },
        {
          text: 'RAB Jasa',
          value: 'rab_j',
        },
        {
          text: 'RAB Material',
          value: 'rab_m',
        },
      ],

      create_data: {
        nomor_prk: '',
        nama_project: '',
        nomor_lot: '',
        prioritas: '',
        type: 1
      },

      create_data_error: {
        nomor_prk: null,
      },

      create_dialog: false,
      prioritas_options: [1, 2, 3, 4],

      snackbar: false,
      snackbar_text: '',

      import_dialog: false,
    }
  },
  mounted() {
    this.basket_no = this.$route.params.basket
    this.getData()
  },
  methods: {
    getData() {
      this.$axios.get(`/v1/prk?basket=${this.basket_no}`)
        .then(res => {
          this.prks = res.data.data
        })
    },
    createSave() {
      if(!this.hasPermission) {
        this.snackbar_text = 'Anda tidak memiliki akses untuk mengubah PRK.';
        this.snackbar = true;
        return this.getData();
      }

      // reset error
      this.create_data_error = {
        nomor_prk: null
      }

      this.create_data.basket = this.$route.params.basket;
      this.$axios.post(`/v1/prk`, this.create_data)
        .then(res => {
          this.$router.push(`/tahapan/prk/${this.$route.params.basket}/${res.data.data.id}`)

          this.create_data = {
            nomor_prk: '',
            nama_project: '',
            nomor_lot: '',
            prioritas: '',
            type: 1
          }

          this.create_dialog = false;

          this.snackbar_text = 'PRK berhasil disimpan.';
          this.snackbar = true;
          return this.getData();
        })
        .catch(e => {
          switch (e.response.status) {
            case 400:
              this.snackbar_text = e.response.status.message;
              break;
            case 422:
              this.create_data_error = e.response.data
              this.snackbar_text = 'Periksa kembali data Anda.';
              break;
            case 500:
              this.snackbar_text = 'Terjadi kesalahan.';
            default:
              break;
          }
          
          this.snackbar = true;
        })
    },
    createCancel() {
      this.create_data = {
        nomor_prk: '',
        nama_project: '',
        nomor_lot: '',
        prioritas: '',
        type: 1
      }

      this.create_dialog = false
    },
    setBasket(basket) {
      this.basket_no = basket
    },
    doneImport() {
      this.import_dialog = false;
      this.snackbar_text = 'Import PRK berhasil.';
      this.snackbar = true;
      this.getData();
    },
    errorImport(e) {
      if(e.message) {
        this.snackbar_text = e.message;
      } else {
        this.snackbar_text = e;
      }
      this.snackbar = true;
      this.getData();
    }
  },
  computed: {
    hasPermission() {
      return this.$auth.user.permissions.includes('edit-prk') || this.$auth.user.permissions.includes('superadmin');
    }
  }
}
</script>

<style>

</style>