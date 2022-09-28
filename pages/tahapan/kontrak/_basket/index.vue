<template>
<div class="container">
  <div class="d-flex justify-end">
    <v-breadcrumbs :items="breadcrumbItems"></v-breadcrumbs>
  </div>

  <div class="mb-5">
    <page-title title="KONTRAK" description="List project dalam tahap kontrak" />
  </div>

  <v-card elevation="4" class="rounded-lg mb-5">
    <v-tabs 
      hide-slider 
      color="yellow"
      background-color="light-blue"
      dark
    >
      <v-tab nuxt exact :key="1" to="/tahapan/kontrak/1">BASKET 1</v-tab>
      <v-tab nuxt exact :key="2" to="/tahapan/kontrak/2">BASKET 2</v-tab>
      <v-tab nuxt exact :key="3" to="/tahapan/kontrak/3">BASKET 3</v-tab>
    </v-tabs>
  </v-card>
  
  <v-card class="rounded-lg" elevation="4">
    <v-card-title>
      <v-text-field 
        style="max-width: 300px" 
        v-model="search" 
        append-icon="mdi-magnify" 
        label="Cari" 
        single-line
        hide-details
        dense
        outlined
        class="rounded-lg"
      ></v-text-field>
    </v-card-title>
    <v-data-table :headers="headers" :items="pengadaans" :loading="loading" class="elevation-1">
      <template v-slot:item.nomor_kontrak="{ item }">
        <nuxt-link :to="'/tahapan/kontrak/'+basket_no+'/'+item.id">
          {{ item.nomor_kontrak }}
        </nuxt-link>
      </template>
      <template v-slot:item.direksi="{ item }">
        {{ parseDireksi(item.direksi) }}
      </template>
      <template v-slot:item.type="{ item }">
        {{ item.type == 1 ? 'MURNI' : 'LUNCURAN' }}
      </template>
    </v-data-table>
  </v-card>
</div>
</template>
<script>
import PageTitle from '~/components/PageTitle.vue'
export default {
  components: { PageTitle },

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
          text: 'KONTRAK',
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
          text: 'Nomor Kontrak',
          align: 'start',
          value: 'nomor_kontrak',
        },
        {
          text: 'Tanggal Kontrak',
          value: 'tanggal_kontrak',
        },
        {
          text: 'Tanggal Awal',
          value: 'tanggal_awal',
        },
        {
          text: 'Tanggal Akhir',
          value: 'tanggal_awal',
        },
        {
          text: 'Pelaksana',
          value: 'pelaksana',
        },
        {
          text: 'Direksi',
          value: 'direksi',
        },
        {
          text: 'Tipe',
          value: 'type',
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
      this.$axios.get(`/v1/kontrak?basket=${this.basket_no}`)
        .then(res => {
          this.pengadaans = res.data.data
        })
    },
    parseDireksi(direksi) {
      let direksi_array = [
        'PERENCANAAN', 
        'KONSTRUKSI',
        'JARINGAN',
        'TRANSAKSI ENERGI',
        'NIAGA',
        'PEMASARAN',
        'KEUANGAN DAN UMUM',
        'K3L'];
      return direksi_array[direksi-1];
    }
  }
}
</script>

<style>

</style>