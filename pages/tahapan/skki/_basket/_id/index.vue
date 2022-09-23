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
      <skki-navbar active="informasi" :has-permission="hasPermission"/>

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
                    label="Nomor SKKI"
                    v-model="item.nomor_skki"
                    v-debounce="updateData"
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
                    label="Nomor PRK-SKKI"
                    v-model="item.nomor_prk_skki"
                    v-debounce="updateData"
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
                    v-debounce="updateData"
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
                  label="Nomor WBS Jasa"
                  v-model="item.nomor_wbs_jasa"
                  v-debounce="updateData"
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
                label="Nomor WBS Material"
                v-model="item.nomor_wbs_material"
                v-debounce="updateData"
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
                label="Total RAB Jasa"
                readonly
                prefix="Rp"
                :value="item.rab_jasa ? new Intl.NumberFormat('id-ID').format(item.rab_jasa.total) : 0"
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
                :value="item.rab_material ? new Intl.NumberFormat('id-ID').format(item.rab_material.total) : 0"
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
import SkkiNavbar from '~/components/SkkiNavbar.vue';
const randomstring = require('randomstring')
export default {
  components: {
    Breadcrumbs,
    PageTitle,
    SkkiNavbar,
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
    this.id = this.$route.params.id;
    this.$axios.get(`/v1/skki/${this.$route.params.id}`)
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
              text: 'SKKI',
              disabled: false,
              href: '/tahapan/skki/1',
            },
            {
              text: res.data.data.nomor_prk_skki,
              disabled: true,
              href: '/tahapan/skki/'+res.data.data.id,
            }
          ];

          this.page_title = {
            basket      : res.data.data.basket,
            title       : res.data.data.nomor_prk_skki,
            description : res.data.data.nama_project
          }
        })
    this.getData()
  },
  methods: {
    getData() {
      this.$axios.get(`/v1/skki/${this.id}`)
        .then(res => {
          this.item = res.data.data
        })
        .catch(e => {
          this.snackbar_text = e.response.data.message,
          this.snackbar = true;
        })
        .finally(() => {})
    },
    updateData() {
      if(!this.hasPermission){
        this.snackbar_text = 'Anda tidak memiliki akses untuk memperbarui SKKI.';
        this.snackbar = true;
        return this.getData();
      }

      let data = {
        nomor_skki: this.item.nomor_skki,
        nomor_prk_skki: this.item.nomor_prk_skki,
        nama_project: this.item.nama_project,
        nomor_wbs_jasa: this.item.nomor_wbs_jasa,
        nomor_wbs_material: this.item.nomor_wbs_material,
      }
      this.disabled = true;
      this.$axios.post(`/v1/skki/${this.$route.params.id}`, data)
        .then(res => {
          this.snackbar_text = 'SKKI berhasil diperbarui.';
          this.snackbar = true;
          this.getData()
        })
        .catch(e => {
          alert(JSON.stringify(e.response))
        })
        .finally(() => {
          this.disabled = false
        })
    }
  },
  computed: {
    hasPermission() {
      return this.$auth.user.permissions.includes('edit-skki') || this.$auth.user.permissions.includes('superadmin')
    }
  }
}
</script>

<style>

</style>