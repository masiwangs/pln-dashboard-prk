<template>
  <div>
    <breadcrumbs :items="breadcrumbs_items" />
    <page-title :item="page_title" />

    <div class="mb-5">
      <kontrak-navbar active="informasi" :has-permission="hasPermission"/>

      <v-card elevation="4" class="rounded-lg">
        <v-card-text>
          <v-row>
            <v-col cols="12" sm="6" lg="4">
              <v-text-field outlined hide-details="auto" dense label="Nodin" readonly class="rounded-lg"
                :value="item.pengadaan ? item.pengadaan.nodin : ''"></v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="6" lg="4">
              <v-text-field outlined hide-details="auto" dense label="Nomor Kontrak" class="rounded-lg"
                v-model="item.nomor_kontrak" :readonly="!item.is_amandemen"></v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="6" lg="4">
              <v-text-field outlined hide-details="auto" class="rounded-lg" dense label="Nomor PO"
                v-model="item.nomor_po" :readonly="!item.is_amandemen"></v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="6" lg="4">
              <v-text-field outlined hide-details="auto" class="rounded-lg" dense label="Pelaksana"
                v-model="item.pelaksana" :readonly="!item.is_amandemen"></v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="6" lg="4">
              <v-select :items="direksi_options" label="Direksi" hide-details="auto" class="rounded-lg" dense
                v-model="item.direksi" :readonly="!item.is_amandemen" outlined></v-select>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="6" lg="4">
              <v-text-field outlined hide-details="auto" dense label="Tanggal Kontrak" class="rounded-lg"
                v-model="item.tanggal_kontrak" :readonly="!item.is_amandemen" type="date"></v-text-field>
            </v-col>
            <v-col cols="12" sm="6" lg="4">
              <v-text-field outlined hide-details="auto" dense label="Tanggal Awal" class="rounded-lg"
                v-model="item.tanggal_awal" :readonly="!item.is_amandemen" type="date"></v-text-field>
            </v-col>
            <v-col cols="12" sm="6" lg="4">
              <v-text-field outlined hide-details="auto" dense label="Tanggal Akhir" class="rounded-lg"
                v-model="item.tanggal_akhir" :readonly="!item.is_amandemen" type="date"></v-text-field>
            </v-col>
          </v-row>
          <v-row v-if="item.versi_amandemen">
            <v-col cols="12" sm="6" lg="4">
              <v-text-field outlined hide-details="auto" class="rounded-lg" dense label="Versi Amandemen" readonly
                :value="item.versi_amandemen"></v-text-field>
            </v-col>
          </v-row>
          <v-row v-if="hasPermission">
            <v-col cols="12">
              <v-divider></v-divider>
            </v-col>
          </v-row>
          <v-row v-if="hasPermission">
            <v-spacer></v-spacer>
            <v-btn v-if="item.is_amandemen == 0" elevation="0" dark color="blue" class="me-2 rounded-lg"
              @click="amandemen_dialog = true">Amandemen</v-btn>
            <v-btn v-if="item.is_amandemen == 1" elevation="0" dark color="red" @click="cancelAmandemen"
              class="me-2 rounded-lg">Batal</v-btn>
            <v-btn v-if="item.is_amandemen == 1" elevation="0" dark color="light-blue" class="rounded-lg"
              @click="updateData">Simpan Amandemen</v-btn>
          </v-row>
        </v-card-text>
      </v-card>
    </div>

    <!-- amandemen dialog -->
    <v-dialog v-model="amandemen_dialog" persistent max-width="400">
      <v-card class="rounded-lg">
        <v-card-title class="text-h5">
          Amandemen kontrak?
        </v-card-title>
        <v-card-text>
          <v-text-field outlined hide-details="auto" dense label="Nomor Amandemen" class="rounded-lg"
            v-model="amandemen_data"></v-text-field>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="light" text class="rounded-lg" @click="closeAmandemenDialog">
            Batal
          </v-btn>
          <v-btn color="light-blue" dark elevation="0" class="rounded-lg" @click="saveAmandemen">
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

        item: {},
        disabled: false,
        id: '',
        tab: 'informasi',
        is_amandemen: false,
        direksi_options: [{
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
        amandemen_dialog: false,
        amandemen_data: ''
      }
    },
    mounted() {
      this.id = this.$route.params.id;
      this.getData();
      this.getKontrak();
    },
    methods: {
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
          })
      },
      getData() {
        this.$axios.get(`/v1/kontrak/${this.id}`)
          .then(res => {
            this.item = res.data.data
            this.breadcrumbItems[3].text = this.item.nama_project
            this.breadcrumbItems[3].href = '/tahapan/kontrak/' + this.item.id
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
      updateData() {
        console.log(this.item)
        let id = this.$route.params.id
        let data = {
          nomor_kontrak: this.item.nomor_kontrak,
          nomor_po: this.item.nomor_po,
          pelaksana: this.item.pelaksana,
          direksi: this.item.direksi,
          tanggal_kontrak: this.item.tanggal_kontrak,
          tanggal_awal: this.item.tanggal_awal,
          tanggal_akhir: this.item.tanggal_akhir
        }
        this.disabled = true;
        this.$axios.post(`/v1/kontrak/${id}`, data)
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