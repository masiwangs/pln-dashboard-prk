<template>
  <div>
    <breadcrumbs :items="breadcrumbs_items" />
    <page-title :item="page_title"/>

    <div class="mb-5">
      <pengadaan-navbar active="catatan" :has-permission="hasPermission"/>

      <v-card elevation="4" class="rounded-lg">
        <v-card-text style="max-height: 500px; overflow-y: auto">
          <v-list three-line>
            <template v-for="item in items">
              <v-divider
                :key="item.id"
                inset
              ></v-divider>
              <v-list-item :key="item.id+'-list'">
                <v-list-item-avatar>
                  <v-img :src="item.user.avatar"></v-img>
                </v-list-item-avatar>

                <v-list-item-content>
                  <v-list-item-title v-html="item.user.nama"></v-list-item-title>
                  <v-list-item-subtitle v-html="item.catatan"></v-list-item-subtitle>
                  <small class="grey--text">{{ item.updated_at }}</small>
                </v-list-item-content>
              </v-list-item>
            </template>
          </v-list>
        </v-card-text>
        <v-card-text>
          <v-textarea
            class="mb-3 rounded-lg"
            color="blue"
            outlined
            auto-grow
            name="input-7-4"
            label="Tulis catatan"
            rows="3"
            hide-details="auto"
            v-model="create_data"
          ></v-textarea>
          <div class="d-flex justify-end">
            <v-btn
              elevation="0"
              dark
              color="light-blue"
              class="rounded-lg"
              @click="createSave"
            >Simpan</v-btn>
          </div>
        </v-card-text>
      </v-card>
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

      items: [
      ],

      create_data: '',
      disabled: false
    }
  },
  created() {
    this.id = this.$route.params.id;
  },
  mounted() {
    this.getData()
    this.getPengadaan();
  },
  methods: {
    getPengadaan() {
      this.$axios.get(`/v1/pengadaan/${this.$route.params.id}`)
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
              text: 'Pengadaan',
              disabled: false,
              href: '/tahapan/pengadaan/1',
            },
            {
              text: res.data.data.nodin,
              disabled: true,
              href: '/tahapan/pengadaan/'+res.data.data.id,
            }
          ];

          this.page_title = {
            basket      : res.data.data.basket,
            title       : res.data.data.nodin,
            description : res.data.data.nama_project
          }
        })
    },
    getData() {
      this.$axios.get(`/v1/pengadaan/${this.id}/catatan`)
        .then(res => {
          this.items = res.data.data
        })
        .finally(() => {})
    },
    createSave() {
      this.disabled = true
      let data = {
        catatan: this.create_data
      }
      this.$axios.post(`/v1/pengadaan/${this.id}/catatan`, data)
        .then(res => {
          this.items.push(res.data.data)
          this.create_data = ''
        })
        .finally(() => {
          this.disabled = false
        })
    }
  }
}
</script>

<style>

</style>