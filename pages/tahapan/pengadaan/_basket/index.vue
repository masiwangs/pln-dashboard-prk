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
    <page-title title="PENGADAAN" description="List project dalam tahap pengadaan" />
  </div>

  <v-card class="rounded-lg mb-5" elevation="4">
    <v-tabs 
      hide-slider 
      color="yellow"
      background-color="light-blue"
      dark
    >
      <v-tab nuxt exact :key="1" to="/tahapan/pengadaan/1">BASKET 1</v-tab>
      <v-tab nuxt exact :key="2" to="/tahapan/pengadaan/2">BASKET 2</v-tab>
      <v-tab nuxt exact :key="3" to="/tahapan/pengadaan/3">BASKET 3</v-tab>
    </v-tabs>
  </v-card>
  
  <v-card elevation="4" class="rounded-lg">
    <v-card-title>
      <v-text-field style="max-width: 300px" v-model="search" dense outlined class="rounded-lg" append-icon="mdi-magnify" label="Cari" single-line
        hide-details></v-text-field>
      <v-spacer></v-spacer>
      <v-btn 
        v-show="$auth.user.permissions.includes('edit-pengadaan') || $auth.user.permissions.includes('superadmin')"
        color="green" 
        dark 
        elevation="0" 
        @click="import_dialog = true"
        class="rounded-lg me-2"
        >IMPORT</v-btn>
      <v-btn v-if="hasPermission" color="light-blue" dark elevation="0" class="rounded-lg" @click="create_dialog = true">BARU</v-btn>
    </v-card-title>
    <v-data-table :headers="headers" :items="pengadaans" :loading="loading" class="elevation-1">
      <template v-slot:item.nodin="{ item }">
        <nuxt-link :to="'/tahapan/pengadaan/'+basket_no+'/'+item.id">
          {{ item.nodin }}
        </nuxt-link>
      </template>
      <template v-slot:item.rab_j="{ item }">
        Rp{{ item.wbs_jasa ? new Intl.NumberFormat('id-ID').format(item.wbs_jasa.total) : 0 }}
      </template>
      <template v-slot:item.rab_m="{ item }">
        Rp{{ item.wbs_material ? new Intl.NumberFormat('id-ID').format(item.wbs_material.total) : 0 }}
      </template>
    </v-data-table>
  </v-card>
  
  <!-- create dialog -->
  <v-dialog
      v-model="create_dialog"
      persistent
      max-width="400"
    >
      <v-card class="rounded-lg">
        <v-card-title class="text-h5">
          Tambah Pengadaan Baru
        </v-card-title>
        <v-card-text>
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
              label="Nodin"
              class="mb-3 rounded-lg"
              v-model="create_data.nodin"
          ></v-text-field>
          <v-text-field
              outlined
              hide-details="auto"
              dense
              label="Tanggal Nodin"
              class="mb-3 rounded-lg"
              type="date"
              v-model="create_data.tanggal_nodin"
          ></v-text-field>
          <v-text-field
              outlined
              hide-details="auto"
              dense
              label="Nomor PR Jasa"
              class="mb-3 rounded-lg"
              v-model="create_data.nomor_pr_jasa"
          ></v-text-field>
          <v-autocomplete
            v-model="create_data.skkis"
            :items="skki_options"
            outlined
            dense
            chips
            small-chips
            label="Pilih PRK Besar"
            item-text="nomor_prk_skki"
            hide-details="auto"
            item-value="id"
            multiple
            class="rounded-lg"
          ></v-autocomplete>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="light"
            text
            @click="createCancel"
          >
            Batal
          </v-btn>
          <v-btn
            color="light-blue"
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
      template-url="https://api-monitoring.masiwang.my.id/templates/import_pengadaan_template.xlsx"
      upload-url="/v1/pengadaan/import"
      dialog-title="Import Pengadaan"
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
          href: '/tahapan',
        },
        {
          text: 'PENGADAAN',
          disabled: true,
          href: 'breadcrumbs_link_2',
        },
      ],

      loading: false,
      basket_no: 1,
      pengadaans: [],
      search: '',
      headers: [
        {
          text: 'Nodin',
          align: 'start',
          value: 'nodin',
        },
        {
          text: 'Tanggal Nodin',
          value: 'tanggal_nodin',
        },
        {
          text: 'Nama Project',
          align: 'start',
          value: 'nama_project',
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
        nama_project: '',
        nodin: '',
        tanggal_nodin: '',
        nomor_pr_jasa: '',
        skkis: []
      },
      create_dialog: false,
      skki_options: [],

      snackbar_text: '',
      snackbar: false,

      import_dialog: false
    }
  },
  mounted() {
    this.basket_no = this.$route.params.basket
    this.getData();
    this.getSkkiOptions();
  },
  methods: {
    getData() {
      this.$axios.get(`/v1/pengadaan?basket=${this.basket_no}`)
        .then(res => {
          this.pengadaans = res.data.data
        })
    },
    getSkkiOptions() {
      this.$axios.get(`/v1/skki?basket=${this.basket_no}`)
        .then(res => {
          this.skki_options = res.data.data
        })
    },
    createSave() {
      this.create_data.basket = this.basket_no
      this.$axios.post(`/v1/pengadaan`, this.create_data)
        .then(res => {
          this.create_data = {
            nama_project: '',
            nodin: '',
            tanggal_nodin: '',
            nomor_pr_jasa: '',
            skkis: []
          };
          this.create_dialog = false
          this.$router.push('/tahapan/pengadaan/'+this.basket_no+'/'+res.data.data.id)
        })
    },
    createCancel() {
      this.create_data = {
            nama_project: '',
            nodin: '',
            tanggal_nodin: '',
            nomor_pr_jasa: '',
            skkis: []
          };
      this.create_dialog = false
    },
    setBasket(basket) {
      this.basket_no = basket
    },
    doneImport() {
      this.import_dialog = false;
      this.snackbar_text = 'Import SKKI berhasil.';
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
      return this.$auth.user.permissions.includes('edit-pengadaan') || this.$auth.user.permissions.includes('superadmin');
    }
  }
}
</script>

<style>

</style>