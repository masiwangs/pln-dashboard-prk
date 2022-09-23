<template>
  <div>
    <breadcrumbs :items="breadcrumbs_items" />
    <page-title :item="page_title"/>

    <div class="mb-5">
      <skki-navbar active="prk" :has-permission="hasPermission"/>

      <v-card
        elevation="4"
        class="rounded-lg"
      >
          <v-card-title>
              <v-text-field
                  style="max-width: 300px"
                  v-model="search"
                  append-icon="mdi-magnify"
                  label="Cari"
                  single-line
                  hide-details
                  outlined
                  dense
                  class="rounded-lg"
              ></v-text-field>
              <v-spacer></v-spacer>
              <v-btn
                  v-if="hasPermission"
                  dark
                  color="light-blue"
                  class="rounded-lg"
                  elevation="0"
                  @click="create_dialog = true"
              >Baru</v-btn>
          </v-card-title>
          <v-data-table
              :headers="headers"
              :items="items"
              :items-per-page="5"
              class="elevation-0"
          >
              <template v-slot:item.nomor_prk="{ item }">
                  <nuxt-link :to="'/tahapan/prk/'+item.prk.basket+'/'+item.prk.id">
                    {{ item.prk.nomor_prk }}
                  </nuxt-link>
              </template>
              <template v-slot:item.rab_jasa="{ item }">
                  Rp{{ item.prk.rab_jasa ? new Intl.NumberFormat('id-ID').format(item.prk.rab_jasa.total) : 0 }}
              </template>
              <template v-slot:item.rab_material="{ item }">
                  Rp{{ item.prk.rab_material ? new Intl.NumberFormat('id-ID').format(item.prk.rab_material.total) : 0 }}
              </template>
              <template v-slot:item.menu="{ item }">
                <div class="d-flex justify-end">
                  <v-btn
                      small
                      text
                      color="red"
                      @click="openDeleteDialog(item.id)"
                  >
                      <v-icon>mdi-delete</v-icon>
                  </v-btn>
                </div>
              </template>
          </v-data-table>
      </v-card>

      <!-- dialog baru -->
      <v-dialog
        v-model="create_dialog"
        persistent
        max-width="400"
        class="rounded-lg"
      >
        <v-card>
          <v-card-title class="text-h5">
            Tambah PRK Baru
          </v-card-title>
          <v-card-text>
              <v-row>
                  <v-col cols="10" sm="auto">
                      <v-autocomplete
                          outlined
                          :items="prk_options"
                          item-text="nomor_prk"
                          item-value="id"
                          hide-details="auto"
                          dense
                          label="Nomor PRK"
                          v-model="create_data[0].prk_id"
                          class="rounded-lg"
                      ></v-autocomplete>
                  </v-col>
                  <v-col cols="auto" class="d-none d-sm-block">
                      <v-btn
                          dark
                          text
                          @click="clearCreateData(0)"
                          class="rounded-lg"
                      >
                          <v-icon color="red">mdi-delete</v-icon>
                      </v-btn>
                  </v-col>
              </v-row>
              <v-row class="d-none d-sm-flex">
                  <v-col cols="10" sm="auto">
                      <v-autocomplete
                          outlined
                          :items="prk_options"
                          item-text="nomor_prk"
                          item-value="id"
                          hide-details="auto"
                          dense
                          label="Nomor PRK"
                          v-model="create_data[1].prk_id"
                          class="rounded-lg"
                      ></v-autocomplete>
                  </v-col>
                  <v-col cols="auto">
                      <v-btn
                          dark
                          text
                          @click="clearCreateData(1)"
                          class="rounded-lg"
                      >
                          <v-icon color="red">mdi-delete</v-icon>
                      </v-btn>
                  </v-col>
              </v-row>
              <v-row class="d-none d-sm-flex">
                  <v-col cols="10" sm="auto">
                      <v-autocomplete
                          outlined
                          :items="prk_options"
                          item-text="nomor_prk"
                          item-value="id"
                          hide-details="auto"
                          dense
                          label="Nomor PRK"
                          v-model="create_data[2].prk_id"
                          class="rounded-lg"
                      ></v-autocomplete>
                  </v-col>
                  <v-col cols="auto">
                      <v-btn
                          dark
                          text
                          @click="clearCreateData(2)"
                          class="rounded-lg"
                      >
                          <v-icon color="red">mdi-delete</v-icon>
                      </v-btn>
                  </v-col>
              </v-row>
              <v-row class="d-none d-sm-flex">
                  <v-col cols="10" sm="auto">
                      <v-autocomplete
                          outlined
                          :items="prk_options"
                          item-text="nomor_prk"
                          item-value="id"
                          hide-details="auto"
                          dense
                          label="Nomor PRK"
                          v-model="create_data[3].prk_id"
                          class="rounded-lg"
                      ></v-autocomplete>
                  </v-col>
                  <v-col cols="auto">
                      <v-btn
                          dark
                          text
                          @click="clearCreateData(3)"
                          class="rounded-lg"
                      >
                          <v-icon color="red">mdi-delete</v-icon>
                      </v-btn>
                  </v-col>
              </v-row>
              <v-row class="d-none d-sm-flex">
                  <v-col cols="10" sm="auto">
                      <v-autocomplete
                          outlined
                          :items="prk_options"
                          item-text="nomor_prk"
                          item-value="id"
                          hide-details="auto"
                          dense
                          label="Nomor PRK"
                          v-model="create_data[4].prk_id"
                          class="rounded-lg"
                      ></v-autocomplete>
                  </v-col>
                  <v-col cols="auto">
                      <v-btn
                          dark
                          text
                          @click="clearCreateData(4)"
                          class="rounded-lg"
                      >
                          <v-icon color="red">mdi-delete</v-icon>
                      </v-btn>
                  </v-col>
              </v-row>
              
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn
              color="light"
              text
              @click="closeCreateDialog"
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

      <!-- dialog delete -->
      <v-dialog
        v-model="delete_dialog"
        persistent
        max-width="400"
      >
        <v-card>
          <v-card-title class="text-h5">
            Hapus PRK?
          </v-card-title>
          <v-card-text>
              Apakah Anda yakin menghapus PRK dari SKKI ini? <br/>
              Data yang telah dihapus tidak dapat dikembalikan.
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn
              color="light"
              text
              @click="closeDeleteDialog"
            >
              Batal
            </v-btn>
            <v-btn
              color="red"
              text
              @click="deleteSave"
            >
              Hapus
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </div>
  </div>
</template>

<script>
import Breadcrumbs          from '~/components/header/Breadcrumbs.vue';
import PageTitle            from '~/components/header/PageTitle.vue';
import PrkNavbar from '~/components/PrkNavbar.vue';

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

      search: '',
      
      items: [],
      create_dialog: false,
      delete_dialog: false,
      create_data: [{
          prk_id: ''
        },
        {
          prk_id: ''
        },
        {
          prk_id: ''
        },
        {
          prk_id: ''
        },
        {
          prk_id: ''
        },
      ],
      disabled: false,
      table_key: '1',
      delete_data: '',
      prk_options: [],
      basket_no: 1
    }
  },
  mounted() {
    this.id = this.$route.params.id;
    this.basket_no = this.$route.params.basket;
    this.getData();
    this.getPrkOptions();
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
  },
  methods: {
    getData() {
      this.$axios.get(`/v1/skki/${this.id}/prk`)
        .then(res => {
          this.items = res.data.data
        })
        .catch(e => {
          this.snackbar_text = e.response.data.message,
          this.snackbar = true;
        })
        .finally(() => {})
    },
    getPrkOptions() {
      this.$axios.get('/v1/prk?basket='+this.basket_no)
        .then(res => {
          this.prk_options = res.data.data
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
    createSave() {
      let id = this.$route.params.id
      let data = this.create_data;
      this.disabled = true;
      this.$axios.post(`/v1/skki/${id}/prk`, data)
        .then(res => {
          this.getData()
          this.closeCreateDialog()
        })
        .catch(e => {
          this.snackbar_text = e.response.data.message,
          this.snackbar = true;
        })
        .finally(() => {
          this.disabled = false;
        })
    },
    closeCreateDialog() {
      this.create_data = [{
          prk_id: ''
        },
        {
          prk_id: ''
        },
        {
          prk_id: ''
        },
        {
          prk_id: ''
        },
        {
          prk_id: ''
        },
      ];

      this.create_dialog = false
    },
    clearCreateData(index) {
      this.create_data[index].prk_id = '';
    },
    openDeleteDialog(item_id) {
      this.delete_data = item_id;
      this.delete_dialog = true;
    },
    closeDeleteDialog() {
      this.delete_data = '';
      this.delete_dialog = false;
    },
    deleteSave() {
      let skki_id = this.$route.params.id;
      let skki_prk_id = this.delete_data;

      this.disabled = true;
      this.$axios.delete(`/v1/skki/${skki_id}/prk/${skki_prk_id}`)
        .then(res => {
          this.getData()
          this.closeDeleteDialog()
        })
        .finally(() => {
          this.disabled = false
        })
    }
  },
  computed: {
    hasPermission() {
      return this.$auth.user.permissions.includes('edit-skki') || this.$auth.user.permissions.includes('superadmin');
    },
    headers() {
      let headers = [{
          text: 'Nomor PRK',
          align: 'start',
          value: 'nomor_prk',
        },
        {
          text: 'Nama Project',
          value: 'prk.nama_project',
        },
        {
          text: 'WBS Jasa',
          value: 'rab_jasa',
        },
        {
          text: 'WBS Material',
          value: 'rab_material',
        }
      ];

      if(this.hasPermission) {
        headers.push({
          text: '',
          sortable: false,
          align: 'end',
          value: 'menu',
        });
      }

      return headers;
    }
  }
}
</script>

<style>

</style>