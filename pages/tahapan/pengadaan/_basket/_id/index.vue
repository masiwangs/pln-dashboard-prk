<template>
  <div>
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

    <breadcrumbs :items="breadcrumbs_items" />
    <page-title :item="page_title"/>

    <div class="mb-5">
      <pengadaan-navbar active="informasi" :has-permission="hasPermission"/>

      <v-card elevation="4" class="rounded-lg">
        <v-card-text>
          <v-row>
            <v-col cols="12" sm="6" lg="4">
              <v-text-field outlined class="rounded-lg" hide-details="auto" dense label="Nama Project" v-model="item.nama_project"
                v-debounce="updateData" :disabled="disabled"></v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="6" lg="4">
              <v-text-field outlined class="rounded-lg" hide-details="auto" dense label="Nodin" v-model="item.nodin"
                v-debounce="updateData" :disabled="disabled"></v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="6" lg="4">
              <v-text-field outlined class="rounded-lg" hide-details="auto" dense label="Tanggal Nodin" v-model="item.tanggal_nodin"
                v-debounce="updateData" :disabled="disabled" type="date"></v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="6" lg="4">
              <v-text-field outlined class="rounded-lg" hide-details="auto" dense label="Nomor PR Jasa" v-model="item.nomor_pr_jasa"
                v-debounce="updateData" :disabled="disabled"></v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="6" lg="4">
              <v-select :items="status_options" class="rounded-lg" label="Status" hide-details="auto" dense v-model="item.status"
                :disabled="disabled" outlined @change="updateStatus"></v-select>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="6" lg="4">
              <v-text-field outlined hide-details="auto" class="rounded-lg" dense label="Total RAB Jasa" readonly prefix="Rp"
                :value="item.wbs_jasa ? new Intl.NumberFormat('id-ID').format(item.wbs_jasa.total) : 0"></v-text-field>
            </v-col>
            <v-col cols="12" sm="6" lg="4">
              <v-text-field outlined hide-details="auto" class="rounded-lg" dense label="Total RAB Material" prefix="Rp"
                :value="item.wbs_material ? new Intl.NumberFormat('id-ID').format(item.wbs_material.total) : 0"
                readonly></v-text-field>
            </v-col>
          </v-row>
        </v-card-text>
      </v-card>
    </div>

    <v-dialog v-model="kontrak_dialog" persistent max-width="400">
      <v-card>
        <v-card-title class="text-h5">
          Kontrak Baru
        </v-card-title>
        <v-card-text>
          <v-text-field 
            outlined 
            hide-details="auto" 
            dense 
            label="Nomor Kontrak" 
            v-model="kontrak_data.nomor_kontrak"
            class="mb-3"
            :disabled="disabled"></v-text-field>
          <v-text-field 
            outlined 
            hide-details="auto" 
            dense 
            label="Tanggal Kontrak" 
            v-model="kontrak_data.tanggal_kontrak"
            type="date"
            class="mb-3"
            :disabled="disabled"></v-text-field>
          <v-text-field 
            outlined 
            hide-details="auto" 
            dense 
            label="Tanggal Awal" 
            v-model="kontrak_data.tanggal_awal"
            type="date"
            class="mb-3"
            :disabled="disabled"></v-text-field>
          <v-text-field 
            outlined 
            hide-details="auto" 
            dense 
            label="Tanggal Akhir" 
            v-model="kontrak_data.tanggal_akhir"
            type="date"
            class="mb-3"
            :disabled="disabled"></v-text-field>
          <v-text-field 
            outlined 
            hide-details="auto" 
            dense 
            label="Pelaksana" 
            v-model="kontrak_data.pelaksana"
            class="mb-3"
            :disabled="disabled"></v-text-field>
          <v-select 
            :items="direksi_options" 
            label="Direksi" 
            hide-details="auto" 
            dense 
            v-model="kontrak_data.direksi"
            :disabled="disabled" 
            outlined></v-select>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="light" text :disabled="disabled" @click="closeKontrakDialog">
            Batal
          </v-btn>
          <v-btn color="blue" text :disabled="disabled" @click="kontrakSave">
            SIMPAN
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
import Breadcrumbs          from '~/components/header/Breadcrumbs.vue';
import PageTitle            from '~/components/header/PageTitle.vue';
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

      item: {},
      disabled: false,
      id: '',
      tab: 'informasi',
      status_options: [
        {
          text: 'Proses',
          value: 1
        },
        {
          text: 'Terkontrak',
          value: 2
        },
      ],
      kontrak_dialog: false,
      kontrak_data: {},
      direksi_options: [
        {
          value: 1,
          text: 'PERENCANAAN'
        },
        {
          value: 2,
          text: 'KONSTRUKSI'
        },
        {
          value: 3,
          text: 'JARINGAN'
        },
        {
          value: 4,
          text: 'TRANSAKSI ENERGI'
        },
        {
          value: 5,
          text: 'NIAGA'
        },
        {
          value: 6,
          text: 'PEMASARAN'
        },
        {
          value: 7,
          text: 'KEUANGAN DAN UMUM'
        },
        {
          value: 8,
          text: 'K3L'
        },
      ],
      snackbar_text: '',
      snackbar:false
    }
  },
  mounted() {
    this.id = this.$route.params.id;
    this.getData()
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
              href: '/tahapan/pengadaan/'+res.data.data.id,
            }
          ];

          this.page_title = {
            basket      : res.data.data.basket,
            title       : res.data.data.nodin,
            description : res.data.data.nama_project
          }
        })
  },
  methods: {
    getData() {
      this.$axios.get(`/v1/pengadaan/${this.id}`)
        .then(res => {
          this.item = res.data.data
          this.breadcrumbItems[3].text = this.item.nama_project
          this.breadcrumbItems[3].href = '/tahapan/pengadaan/'+this.item.id
        })
        .finally(() => {})
    },
    updateData() {
      if(!this.hasPermission) {
        this.snackbar_text = 'Anda tidak memiliki akses untuk memperbarui pengadaan.';
        this.snackbar = true;
        return this.getData();
      }
      let id = this.$route.params.id
      let data = {
        nodin         : this.item.nodin,
        tanggal_nodin : this.item.tanggal_nodin,
        nama_project  : this.item.nama_project,
        nomor_pr_jasa : this.item.nomor_pr_jasa
      }
      this.disabled = true;
      this.$axios.post(`/v1/pengadaan/${id}`, data)
        .then(res => {
          this.getData()
        })
        .catch(e => {
          alert(JSON.stringify(e.response))
        })
        .finally(() => {
          this.disabled = false
        })
    },
    updateStatus() {
      if(this.item.status == 2) {
        this.kontrak_dialog = true
      }
    },
    kontrakSave(){
      if(!this.hasPermission) {
        this.snackbar_text = 'Anda tidak memiliki akses untuk memperbarui pengadaan.';
        this.snackbar = true;
        this.kontrak_dialog = false;
        return this.getData();
      }
      let data = {
        nomor_kontrak   : this.kontrak_data.nomor_kontrak,
        tanggal_kontrak : this.kontrak_data.tanggal_kontrak,
        tanggal_awal    : this.kontrak_data.tanggal_awal,
        tanggal_akhir   : this.kontrak_data.tanggal_akhir,
        pelaksana       : this.kontrak_data.pelaksana,
        direksi         : this.kontrak_data.direksi,
        pengadaan_id    : this.$route.params.id,
        basket          : this.$route.params.basket
      }
      this.$axios.post(`/v1/kontrak`, data)
        .then(res => {
          this.$router.push('/tahapan/kontrak/'+this.$route.params.basket+'/'+res.data.data.id)
        })
    },
    closeKontrakDialog(){
      this.kontrak_data = {}
      this.kontrak_dialog = false;
    }
  },
  computed: {
    hasPermission() {
      return this.$auth.user.permissions.includes('edit-pengadaan') || this.$auth.user.permissions.includes('superadmin');
    },
    versi_amandemen() {
      return this.item.versi_amandemen ? true : false;
    }
  }
}
</script>

<style>

</style>