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
      <pelaksanaan-navbar active="informasi" :has-permission="hasPermission"/>

      <v-card elevation="4" class="rounded-lg">
        <v-card-text>
          <v-row>
            <v-col cols="12" sm="6" lg="4">
              <v-text-field 
                outlined 
                hide-details="auto" 
                dense 
                label="Nomor Kontrak" 
                readonly 
                :value="item.nomor_kontrak"
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
                label="Nomor SPK" 
                v-model="item.spk"
                v-debounce="updateData"
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
                label="Progress" 
                v-model="item.progress"
                v-debounce="updateData"
                min="0"
                max="100"
                type="number"
                step="0.1"
                suffix="%"
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
                label="Nomor PR Material" 
                v-model="item.nomor_pr_material"
                v-debounce="updateData"
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
import PelaksanaanNavbar from '~/components/PelaksanaanNavbar.vue';
const randomstring = require('randomstring')
export default {
  components: {
    Breadcrumbs,
    PageTitle,
    PelaksanaanNavbar,
  },

  data() {
    return {
      breadcrumbs_items: [],
      page_title: {},

      item: {},
      disabled: false,
      tab: 'informasi',

      snackbar: false,
      snackbar_text: ''
    }
  },
  mounted() {
    this.getData();
  },
  methods: {
    getData() {
      this.$axios.get(`/v1/pelaksanaan/${this.$route.params.id}`)
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
              text: 'Pelaksanaan',
              disabled: false,
              href: '/tahapan/pelaksanaan/1',
            },
            {
              text: res.data.data.spk,
              disabled: true,
              href: '/tahapan/pelaksanaan/'+res.data.data.id,
            }
          ];

          this.item = res.data.data;

          this.page_title = {
            basket      : res.data.data.basket,
            title       : res.data.data.spk,
            description : res.data.data.pelaksana
          }
        })
    },
    updateData() {
      if(!this.hasPermission) {
        this.snackbar_text = 'Anda tidak memiliki akses untuk memperbarui pelaksanaan.';
        this.snackbar = true;
        return this.getData();
      }

      let data = {
        spk               : this.item.spk,
        progress          : this.item.progress,
        nomor_pr_material : this.item.nomor_pr_material
      }
      this.disabled = true;
      this.$axios.post(`/v1/pelaksanaan/${this.$route.params.id}`, data)
        .then(res => {
          this.snackbar_text = 'Pelaksanaan berhasil diperbarui.';
          this.snackbar = true;
          this.getData()
        })
        .catch(e => {
          alert(JSON.stringify(e.response))
        })
        .finally(() => {
          this.disabled = false
        })
    },
  },
  computed: {
    hasPermission() {
      return this.$auth.user.permissions.includes('edit-pelaksanaan') || this.$auth.user.permissions.includes('superadmin');
    }
  }
}
</script>

<style>

</style>