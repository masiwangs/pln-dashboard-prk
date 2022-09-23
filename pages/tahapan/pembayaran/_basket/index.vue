<template>
  <div class="container">
    <div class="d-flex justify-end">
      <v-breadcrumbs :items="breadcrumbItems"></v-breadcrumbs>
    </div>

    <div class="mb-5">
      <page-title title="PEMBAYARAN" description="List project dalam tahap pelaksanaan" />
    </div>

    <v-card class="rounded-lg mb-5" elevation="4">
      <v-tabs hide-slider color="yellow" background-color="light-blue" dark>
        <v-tab nuxt exact :key="1" to="/tahapan/pembayaran/1">BASKET 1</v-tab>
        <v-tab nuxt exact :key="2" to="/tahapan/pembayaran/2">BASKET 2</v-tab>
        <v-tab nuxt exact :key="3" to="/tahapan/pembayaran/3">BASKET 3</v-tab>
      </v-tabs>
    </v-card>

    <v-card class="rounded-lg" elevation="4">
      <v-card-title>
        <v-text-field style="max-width: 300px" class="rounded-lg" dense outlined v-model="search" append-icon="mdi-magnify" label="Cari" single-line
          hide-details></v-text-field>
      </v-card-title>
      <v-data-table :headers="headers" :items="pembayarans" :loading="loading" class="elevation-1">
        <template v-slot:item.nomor_kontrak="{ item }">
          <nuxt-link :to="'/tahapan/pembayaran/'+basket_no+'/'+item.id">
            {{ item.nomor_kontrak }}
          </nuxt-link>
        </template>
        <template v-slot:item.tagihan="{ item }">
          Rp{{ new Intl.NumberFormat('id-ID').format(item.tagihan) }}
        </template>
        <template v-slot:item.terbayar="{ item }">
          Rp{{ new Intl.NumberFormat('id-ID').format(item.terbayar ? item.terbayar.total : 0) }}
          ({{ item.terbayar ? new Intl.NumberFormat('id-ID', {maximumFractionDigits: 2}).format(item.terbayar.total/(item.tagihan ? item.tagihan : 1)) : 0 }}%)
        </template>
        <template v-slot:item.status="{ item }">
          {{ parseStatus(item.status) }}
        </template>
      </v-data-table>
    </v-card>
  </div>
</template>
<script>
  import PageTitle from '~/components/PageTitle.vue'
  export default {
    components: {
      PageTitle
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
            text: 'PELAKSANAAN',
            disabled: true,
            href: 'breadcrumbs_link_2',
          },
        ],

        loading: false,
        basket_no: 1,
        pembayarans: [],
        search: '',
        headers: [{
            text: 'Nomor Kontrak',
            align: 'start',
            value: 'nomor_kontrak',
          },
          {
            text: 'SPK',
            value: 'spk',
          },
          {
            text: 'Tagihan',
            value: 'tagihan',
          },
          {
            text: 'Terbayar',
            value: 'terbayar',
          },
          {
            text: 'Status',
            value: 'status',
          },
        ]
      }
    },
    mounted() {
      this.basket_no = this.$route.params.basket
      this.getData();
    },
    methods: {
      getData() {
        this.$axios.get(`/v1/pembayaran?basket=${this.basket_no}`)
          .then(res => {
            this.pembayarans = res.data.data
          })
      },
      parseStatus(status) {
        let status_array = [
          'DALAM PELAKSANAAN',
          'ADMINISTRASI PROYEK',
          'OUTSTANDING',
          'SELESAI BAYAR',
        ];
        return status_array[status - 1];
      }
    },

  }
</script>

<style>

</style>