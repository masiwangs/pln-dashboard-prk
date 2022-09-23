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
      <prk-navbar active="informasi" :has-permission="hasPermission"/>

      <v-card
        elevation="4"
        class="rounded-lg"
      >
        <v-card-text>
            <v-row>
              <v-col cols="12" sm="6" lg="4">
                  <v-text-field
                  outlined
                  hide-details="auto"
                  dense
                  label="Nomor PRK"
                  v-model="item.nomor_prk"
                  v-debounce="updateSave"
                  :disabled="disabled"
                  class="rounded-lg"
                  ></v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12" sm="6" lg="4">
                  <v-text-field
                  outlined
                  hide-details="auto"
                  dense
                  label="Nama Project"
                  v-model="item.nama_project"
                  v-debounce="updateSave"
                  :disabled="disabled"
                  class="rounded-lg"
                  ></v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12" sm="6" lg="4">
                  <v-text-field
                  outlined
                  hide-details="auto"
                  dense
                  label="Lot"
                  v-model="item.nomor_lot"
                  v-debounce="updateSave"
                  :disabled="disabled"
                  class="rounded-lg"
                  ></v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12" sm="6" lg="4">
                  <v-text-field
                  outlined
                  hide-details="auto"
                  dense
                  label="Prioritas"
                  v-model="item.prioritas"
                  v-debounce="updateSave"
                  :disabled="disabled"
                  class="rounded-lg"
                  ></v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12" sm="6" lg="4">
                <v-select 
                  :items="[{value: 1, text: 'MURNI'}, {value: 2, text: 'LUNCURAN'}]" 
                  label="Tipe" 
                  hide-details="auto" 
                  dense 
                  v-model="item.type"
                  :disabled="disabled" 
                  @change="updateSave"
                  outlined></v-select>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12" sm="6" lg="4">
                  <v-text-field
                  outlined
                  hide-details="auto"
                  dense
                  label="Total RAB Jasa"
                  readonly
                  prefix="Rp"
                  :value="item.rab_jasa ? new Intl.NumberFormat('id-ID').format(item.rab_jasa.rab_jasa) : 0"
                  class="rounded-lg"
                  ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" lg="4">
                  <v-text-field
                  outlined
                  hide-details="auto"
                  dense
                  label="Total RAB Material"
                  prefix="Rp"
                  readonly
                  :value="item.rab_material ? new Intl.NumberFormat('id-ID').format(item.rab_material.rab_material) : 0"
                  class="rounded-lg"
                  ></v-text-field>
              </v-col>
            </v-row>
        </v-card-text>
      </v-card>
    </div>
  </div>
</template>

<script>
import Breadcrumbs          from '~/components/header/Breadcrumbs.vue';
import PageTitle            from '~/components/header/PageTitle.vue';
import PrkNavbar            from '~/components/PrkNavbar.vue';
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

      item: {},

      disabled: false,

      id: '',

      tab: 'informasi',

      snackbar: false,
      snackbar_text: ''
    }
  },
  mounted() {
    this.getData()
  },
  methods: {
    getData() {
      this.$axios.get(`/v1/prk/${this.$route.params.id}`)
        .then(res => {
          // set item
          this.item = res.data.data

          // set breadcrumbs
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

        // set page title
        this.page_title = {
          basket      : res.data.data.basket,
          title       : res.data.data.nomor_prk,
          description : res.data.data.nama_project
        }
        })
        .finally(() => {})
    },
    updateSave() {
      // check user permission
      if(!this.hasPermission) {
        this.snackbar_text = 'Anda tidak memiliki akses untuk memperbarui PRK.';
        this.snackbar = true;
        return this.getData();
      }

      // disable input
      this.disabled = true;

      // init update data
      let data = {
        nomor_prk: this.item.nomor_prk,
        nama_project: this.item.nama_project,
        nomor_lot: this.item.nomor_lot,
        prioritas: this.item.prioritas,
        type: this.item.type,
      }
      // send update data
      this.$axios.post(`/v1/prk/${this.$route.params.id}`, data)
        .then(res => {
          // show snackbar
          this.snackbar_text = 'PRK berhasil diperbarui.';
          this.snackbar = true;
          // get new data
          this.getData();
        })
        .catch(e => {
          // alert error
          alert(JSON.stringify(e.response))
        })
        .finally(() => {
          // enable input
          this.disabled = false
        })
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