<template>
  <div>
    <detail-lampiran-dialog 
      :dialog="detail_dialog" 
      :file="detail_data" 
      tahap="kontrak"
      @close="closeDetailDialog"
      @reload="getLampiran"
    />

    <breadcrumbs :items="breadcrumbs_items" />
    <page-title :item="page_title"/>

    <div class="mb-5">
      <kontrak-navbar active="lampiran" :has-permission="hasPermission"/>

      <v-card elevation="4" class="rounded-lg">
        <v-card-title class="mb-4">
          <v-text-field 
            style="max-width: 300px" 
            v-model="search" 
            append-icon="mdi-magnify" 
            label="Cari" 
            single-line
            hide-details
            v-debounce="getLampiran"
            dense
            outlined
            class="rounded-lg"
          ></v-text-field>
          <v-spacer></v-spacer>
          <upload-dialog tahap="kontrak" @reload="getLampiran" :has-permission="hasPermission"/>
        </v-card-title>

        <v-card-text>
          <v-row>
            <v-col cols="6" sm="3" md="2" lg="2" xl="1" v-for="(lampiran, index) in lampirans" :key="index">
              <v-card 
                elevation="0"
                @click="openDetailDialog(lampiran)"
              >
                <img :src="lampiran.thumbnail" style="max-width: 100%" alt="" srcset="">
                <v-card-text class="text-center">
                  {{ lampiran.nama }}
                </v-card-text>
              </v-card>
            </v-col>
          </v-row>
          <div class="d-flex justify-center mt-5">
            <v-btn
              elevation="0"
              dark
              color="light-blue"
              class="rounded-lg"
              @click="nextPage"
            >Muat lebih banyak</v-btn>
          </div>
        </v-card-text>
      </v-card>
    </div>
  </div>
</template>

<script>
import Breadcrumbs          from '~/components/header/Breadcrumbs.vue';
import PageTitle            from '~/components/header/PageTitle.vue';
import DetailLampiranDialog from '~/components/dialog/DetailLampiranDialog.vue';
import UploadDialog         from '~/components/dialog/UploadDialog.vue';
export default {
  components: {
    Breadcrumbs, 
    DetailLampiranDialog,
    PageTitle,
    UploadDialog, 
  },
  data() {
    return {
      breadcrumbs_items: [],
      page_title: {},
      search: '',
      lampirans: [],
      detail_dialog: false,
      detail_data: {},
      page: 1
    }
  },
  mounted() {
    this.getKontrak()
    this.getLampiran()
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
              href: '/tahapan/kontrak/'+res.data.data.id,
            }
          ];

          this.page_title = {
            basket      : res.data.data.basket,
            title       : res.data.data.nomor_kontrak,
            description : res.data.data.pelaksana
          }
        })
    },
    getLampiran() {
      this.$axios.get(`/v1/kontrak/${this.$route.params.id}/lampiran?search=${this.search}&page=${this.page}`)
        .then(res => {
          if(this.page == 1) {
            return this.lampirans = res.data.data;
          }
          this.lampirans = [...this.lampirans, ...res.data.data]
        })
    },
    openDetailDialog(item) {
      this.detail_data    = item;
      this.detail_dialog  = true;
    },
    closeDetailDialog() {
      this.lampirans = [];
      this.detail_data    = {};
      this.detail_dialog  = false;
    },
    nextPage() {
      this.page++;
      this.getLampiran();
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