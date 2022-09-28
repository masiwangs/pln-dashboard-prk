<template>
<div class="container">
  <div class="d-flex justify-end">
    <v-breadcrumbs :items="breadcrumbItems"></v-breadcrumbs>
  </div>

  <div class="mb-5">
    <page-title title="PELAKSANAAN" description="List project dalam tahap pelaksanaan" />
  </div>

  <v-card elevation="4" class="rounded-lg mb-5">
    <v-tabs 
      hide-slider 
      color="yellow"
      background-color="blue"
      dark
    >
      <v-tab nuxt exact :key="1" to="/tahapan/pelaksanaan/1">BASKET 1</v-tab>
      <v-tab nuxt exact :key="2" to="/tahapan/pelaksanaan/2">BASKET 2</v-tab>
      <v-tab nuxt exact :key="3" to="/tahapan/pelaksanaan/3">BASKET 3</v-tab>
    </v-tabs>
  </v-card>
  
  <v-card elevation="4" class="rounded-lg">
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
        <nuxt-link :to="'/tahapan/pelaksanaan/'+basket_no+'/'+item.id">
          {{ item.nomor_kontrak }}
        </nuxt-link>
      </template>
      <template v-slot:item.progress="{ item }">
        {{ item.progress }}%
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
          text: 'PELAKSANAAN',
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
          text: 'Progress',
          value: 'progress',
        },
        {
          text: 'Tipe',
          value: 'type',
        }
      ]
    }
  },
  mounted() {
    this.basket_no = this.$route.params.basket
    this.getData();
  },
  methods: {
    getData() {
      this.$axios.get(`/v1/pelaksanaan?basket=${this.basket_no}`)
        .then(res => {
          this.pengadaans = res.data.data
        })
    }
  },
  
}
</script>

<style>

</style>