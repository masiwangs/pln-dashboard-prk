<template>
<div class="container">
  <div class="d-flex justify-end">
    <v-breadcrumbs :items="breadcrumbItems"></v-breadcrumbs>
  </div>

  <div class="mb-5">
    <page-title title="Halo, Admin" description="Selamat datang di Dashboard Monitoring" />
  </div>

  <div class="mb-5">
    <dashboard-navbar />
  </div>

  <div>
    <div class="mb-5">
      <v-row>
        <v-col cols="12" sm="6" lg="2">
          <v-card elevation="4" class="rounded-lg">
            <v-card-title class="blue--text subtitle-1">PRK</v-card-title>
            <v-card-text>
              <p class="mb-1"><strong>Rp{{ new Intl.NumberFormat('id-ID').format(stats.prk.total) }}</strong></p>
              <v-divider class="my-2" />
              <p class="mb-0"><strong>Jasa</strong></p>
              <p class="mb-1">Rp{{ new Intl.NumberFormat('id-ID').format(stats.prk.jasa) }}</p>
              <p class="mb-0"><strong>Material</strong></p>
              <p class="mb-0">Rp{{ new Intl.NumberFormat('id-ID').format(stats.prk.material) }}</p>
            </v-card-text>
          </v-card>
        </v-col>
        <v-col cols="12" sm="6" lg="2">
          <v-card elevation="4" class="rounded-lg">
            <v-card-title class="blue--text subtitle-1">SKKI</v-card-title>
            <v-card-text>
              <p class="mb-1"><strong>Rp{{ new Intl.NumberFormat('id-ID').format(stats.skki.total) }}</strong></p>
              <v-divider class="my-2" />
              <p class="mb-0"><strong>Jasa</strong></p>
              <p class="mb-1">Rp{{ new Intl.NumberFormat('id-ID').format(stats.skki.jasa) }}</p>
              <p class="mb-0"><strong>Material</strong></p>
              <p class="mb-0">Rp{{ new Intl.NumberFormat('id-ID').format(stats.skki.material) }}</p>
            </v-card-text>
          </v-card>
        </v-col>
        <v-col cols="12" sm="6" lg="2">
          <v-card elevation="4" class="rounded-lg">
            <v-card-title class="blue--text subtitle-1">PENGADAAN</v-card-title>
            <v-card-text>
              <p class="mb-1"><strong>Rp{{ new Intl.NumberFormat('id-ID').format(stats.pengadaan.total) }}</strong></p>
              <v-divider class="my-2" />
              <p class="mb-0"><strong>Jasa</strong></p>
              <p class="mb-1">Rp{{ new Intl.NumberFormat('id-ID').format(stats.pengadaan.jasa) }}</p>
              <p class="mb-0"><strong>Material</strong></p>
              <p class="mb-0">Rp{{ new Intl.NumberFormat('id-ID').format(stats.pengadaan.material) }}</p>
            </v-card-text>
          </v-card>
        </v-col>
        <v-col cols="12" sm="6" lg="2">
          <v-card elevation="4" class="rounded-lg">
            <v-card-title class="blue--text subtitle-1">KONTRAK</v-card-title>
            <v-card-text>
              <p class="mb-1"><strong>Rp{{ new Intl.NumberFormat('id-ID').format(stats.kontrak.total) }}</strong></p>
              <v-divider class="my-2" />
              <p class="mb-0"><strong>Jasa</strong></p>
              <p class="mb-1">Rp{{ new Intl.NumberFormat('id-ID').format(stats.kontrak.jasa) }}</p>
              <p class="mb-0"><strong>Material</strong></p>
              <p class="mb-0">Rp{{ new Intl.NumberFormat('id-ID').format(stats.kontrak.material) }}</p>
            </v-card-text>
          </v-card>
        </v-col>
        <v-col cols="12" sm="6" lg="2">
          <v-card elevation="4" class="rounded-lg">
            <v-card-title class="blue--text subtitle-1">PELAKSANAAN</v-card-title>
            <v-card-text>
              <p class="mb-1"><strong>Rp{{ new Intl.NumberFormat('id-ID').format(stats.pelaksanaan.total) }}</strong></p>
              <v-divider class="my-2" />
              <p class="mb-0"><strong>Jasa</strong></p>
              <p class="mb-1">Rp{{ new Intl.NumberFormat('id-ID').format(stats.pelaksanaan.jasa) }}</p>
              <p class="mb-0"><strong>Material</strong></p>
              <p class="mb-0">Rp{{ new Intl.NumberFormat('id-ID').format(stats.pelaksanaan.material) }}</p>
            </v-card-text>
          </v-card>
        </v-col>
        <v-col cols="12" sm="6" lg="2">
          <v-card elevation="4" class="rounded-lg">
            <v-card-title class="blue--text subtitle-1">PEMBAYARAN</v-card-title>
            <v-card-text>
              <p class="mb-1"><strong>Rp{{ new Intl.NumberFormat('id-ID').format(stats.pembayaran.total) }}</strong></p>
              <v-divider class="my-2" />
              <p class="mb-0"><strong>&nbsp;</strong></p>
              <p class="mb-1">&nbsp;</p>
              <p class="mb-0"><strong>&nbsp;</strong></p>
              <p class="mb-0">&nbsp;</p>
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>
    </div>

    <div class="mb-5">
      <v-row>
        <v-col cols="12" lg="4">
          <v-card elevation="4" class="rounded-lg">
            <v-card-title style="font-size: 1rem">PERSENTASE KONTRAK/SKKI</v-card-title>
            <v-card-text>
              <h1 class="mb-1">{{ new Intl.NumberFormat('id-ID').format(
                (stats.skki.total || stats.kontrak.total) 
                  ? stats.skki.total > 0 
                    ? Math.round((stats.kontrak.total/stats.skki.total)*100)/100
                    : 100
                  : 0
              ) }}%</h1>
              <p class="mb-0">Rp{{ new Intl.NumberFormat('id-ID').format(stats.kontrak.total) }}/Rp{{ new Intl.NumberFormat('id-ID').format(stats.skki.total) }}</p>
            </v-card-text>
          </v-card>
        </v-col>
        <v-col cols="12" lg="4">
          <v-card elevation="4" class="rounded-lg">
            <v-card-title style="font-size: 1rem">PERSENTASE PEMBAYARAN/SKKI</v-card-title>
            <v-card-text>
              <h1 class="mb-1">{{ new Intl.NumberFormat('id-ID').format(
                  (stats.skki.total || stats.pembayaran.total) 
                  ? stats.skki.total > 0 
                    ? Math.round((stats.pembayaran.total/stats.skki.total)*100)/100
                    : 100
                  : 0
                ) }}%</h1>
              <p class="mb-0">Rp{{ new Intl.NumberFormat('id-ID').format(stats.pembayaran.total) }}/Rp{{ new Intl.NumberFormat('id-ID').format(stats.skki.total) }}</p>
            </v-card-text>
          </v-card>
        </v-col>
        <v-col cols="12" lg="4">
          <v-card elevation="4" class="rounded-lg">
            <v-card-title style="font-size: 1rem">PERSENTASE PEMBAYARAN/KONTRAK</v-card-title>
            <v-card-text>
              <h1 class="mb-1">{{ new Intl.NumberFormat('id-ID').format(
                (stats.kontrak.total || stats.pembayaran.total) 
                  ? stats.kontrak.total > 0 
                    ? Math.round((stats.pembayaran.total/stats.kontrak.total)*100)/100
                    : 100
                  : 0
              ) }}%</h1>
              <p class="mb-0">Rp{{ new Intl.NumberFormat('id-ID').format(stats.pembayaran.total) }}/Rp{{ new Intl.NumberFormat('id-ID').format(stats.kontrak.total) }}</p>
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>
    </div>

    <v-card elevation="4" class="rounded-lg">
      <v-card-title>
        Biaya Proyek
      </v-card-title>
      <v-card-text>
        <apexchart width="100%" height="350px" type="bar" :options="chartOptions" :series="series"></apexchart>
      </v-card-text>
    </v-card>
  </div>
</div>
</template>
<script>
import PageTitle from '~/components/PageTitle.vue'
export default {
  components: {
    PageTitle,
  },
  data() {
    return {
      breadcrumbItems: [{
        text: 'Dashboard',
        disabled: true,
        href: '/',
      }, ],

      tab: null,

      chartOptions: {
        chart: {
          id: 'vuechart-example',
          type: 'bar',
          height: 350
        },
        plotOptions: {
          bar: {
            horizontal: false,
            borderRadius: 10,
            endingShape: 'rounded'
          },
        },
        stroke: {
          show: true,
          width: 2,
          colors: ['transparent']
        },
        dataLabels: {
          enabled: false,
        },
        xaxis: {
          categories: ['PRK', 'SKKI', 'Pengadaan', 'Kontrak', 'Pelaksanaan'],
        },
        yaxis: {
          title: {
            text: 'Rp'
          },
          labels: {
            formatter: function (value) {
              return new Intl.NumberFormat('id-ID').format(value);
            }
          }
        },
        tooltip: {
          y: {
            formatter: function (val) {
              return "Rp " + new Intl.NumberFormat('id-ID').format(val)
            }
          }
        },
      },

      series: [{
        name: 'Jasa',
        data: [0, 0, 0, 0, 0]
      }, {
        name: 'Material',
        data: [0, 0, 0, 0, 0]
      }, {
        name: 'Total',
        data: [0, 0, 0, 0, 0]
      }],

      stats: {
        prk: {
          jasa: 0,
          material: 0,
          total: 0,
        },
        skki: {
          jasa: 0,
          material: 0,
          total: 0,
        },
        pengadaan: {
          jasa: 0,
          material: 0,
          total: 0,
        },
        kontrak: {
          jasa: 0,
          material: 0,
          total: 0,
        },
        pelaksanaan: {
          jasa: 0,
          material: 0,
          total: 0,
        },
        pembayaran: {
          total: 0
        }
      }
    }
  },
  mounted() {
    this.$axios.get('/v1/stats')
      .then(res => {
        this.stats = res.data.data;

        this.series = [{
          name: 'Jasa',
          data: [this.stats.prk.jasa, this.stats.skki.jasa, this.stats.pengadaan.jasa, this.stats.kontrak.jasa, 0]
        }, {
          name: 'Material',
          data: [this.stats.prk.material, this.stats.skki.material, this.stats.pengadaan.material, this.stats.kontrak.material, 0]
        }, {
          name: 'Total',
          data: [this.stats.prk.total, this.stats.skki.total, this.stats.pengadaan.total, this.stats.kontrak.total, this.stats.pembayaran.total]
        }]
      })
  }
}
</script>

<style>

</style>