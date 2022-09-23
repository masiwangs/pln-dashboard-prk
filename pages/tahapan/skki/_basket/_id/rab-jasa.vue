<template>
  <div>
    <breadcrumbs :items="breadcrumbs_items" />
    <page-title :item="page_title"/>

    <div class="mb-5">
      <skki-navbar active="rab-jasa" :has-permission="hasPermission"/>

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
                  dense
                  outlined
                  class="rounded-lg"
              ></v-text-field>
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
              <template v-slot:item.harga="{ item }">
                  Rp{{ new Intl.NumberFormat('id-ID').format(item.harga) }}
              </template>
              <template v-slot:item.menu="{ item }">
                <div class="d-flex justify-end">
                  <v-btn
                      small
                      dark
                      color="amber"
                      elevation="0"
                      @click="openEditDialog(item.id)"
                      class="rounded-lg me-2"
                  >
                      <v-icon>mdi-pencil</v-icon>
                  </v-btn>
                  <v-btn
                      small
                      dark
                      color="red"
                      elevation="0"
                      @click="openDeleteDialog(item.id)"
                      class="rounded-lg"
                  >
                      <v-icon>mdi-delete</v-icon>
                  </v-btn>
                </div>
              </template>
          </v-data-table>
      </v-card>

      <!-- dialog edit -->
      <v-dialog
        v-model="edit_dialog"
        persistent
        max-width="400"
      >
        <v-card
          class="rounded-lg"
        >
          <v-card-title class="text-h5">
            Ubah Jasa
          </v-card-title>
          <v-card-text>
            <v-text-field
              outlined
              hide-details="auto"
              dense
              label="Nama Jasa"
              :disabled="disabled"
              v-model="edit_data.nama_jasa"
              class="mb-3 rounded-lg"
            ></v-text-field>
            <v-text-field
              outlined
              hide-details="auto"
              dense
              label="Harga"
              :disabled="disabled"
              class="rounded-lg"
              v-model="edit_data.harga"
            ></v-text-field>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn
              color="light"
              text
              :disabled="disabled"
              @click="closeEditDialog"
            >
              Batal
            </v-btn>
            <v-btn
              color="light-blue"
              dark
              elevation="0"
              class="rounded-lg"
              :disabled="disabled"
              @click="updateSave"
            >
              SIMPAN
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
        <v-card
          class="rounded-lg"
        >
          <v-card-title class="text-h5">
            Hapus Jasa?
          </v-card-title>
          <v-card-text>
              Apakah Anda yakin menghapus jasa ini? <br/>
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
              dark
              elevation="0"
              class="rounded-lg"
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

      search: '',
      
      items: [],
      edit_dialog: false,
      delete_dialog: false,
      edit_data: {
        id: '',
        nama_jasa: '',
        harga_jasa: ''
      },
      disabled: false,
      table_key: '1',
      delete_data: '',
    }
  },
  created() {
    this.id = this.$route.params.id;
  },
  mounted() {
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
      this.$axios.get(`/v1/skki/${this.id}/jasa`)
        .then(res => {
          this.items = res.data.data
        })
        .catch(e => {
          if(e.response.data.message) {
            alert(e.response.data.message)
          }
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
    openEditDialog(item_id) {
      const jasa = this.items.find(x => x.id == item_id)
      this.edit_data = jasa;
      this.edit_dialog = true
    },
    closeEditDialog() {
      this.edit_data = {
        id: '',
        nama_jasa: '',
        harga: ''
      }
      this.edit_dialog = false
    },
    updateSave() {
      let data = {
        nama_jasa: this.edit_data.nama_jasa,
        harga: this.edit_data.harga
      }

      this.disabled = true;

      this.$axios.post(`/v1/prk/${this.edit_data.prk_id}/jasa/${this.edit_data.id}`, data)
        .then(res => {
          this.getData()
          this.closeEditDialog()
        })
        .finally(() => {
          this.disabled = false;
        })
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
      let prk_id = this.$route.params.id;
      let jasa_id = this.delete_data;

      this.disabled = true;
      this.$axios.delete(`/v1/prk/${this.edit_data.prk_id}/jasa/${this.edit_data.id}`)
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
          text: 'Nama Jasa',
          align: 'start',
          value: 'nama_jasa',
        },
        {
          text: 'Nilai',
          value: 'harga',
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