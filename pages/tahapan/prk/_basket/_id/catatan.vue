<template>
  <div>
    <breadcrumbs :items="breadcrumbs_items" />
    <page-title :item="page_title"/>

    <div class="mb-5">
      <prk-navbar active="rab-jasa" :has-permission="hasPermission"/>

      <v-card
        elevation="4"
        class="rounded-lg"
      >
        <v-card-text style="max-height: 500px; overflow-y: auto">
          <v-list three-line>
            <template v-for="(item, index) in items">
              <v-divider
                inset
              ></v-divider>
              <v-list-item>
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
            color="light-blue"
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
import Breadcrumbs  from '~/components/header/Breadcrumbs.vue';
import PageTitle    from '~/components/header/PageTitle.vue';
import PrkNavbar    from '~/components/PrkNavbar.vue';

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
    this.$axios.get(`/v1/prk/${this.$route.params.id}`)
        .then(res => {
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

          this.page_title = {
            basket      : res.data.data.basket,
            title       : res.data.data.nomor_prk,
            description : res.data.data.nama_project
          }
        })
    this.getData()
  },
  methods: {
    getData() {
      this.$axios.get(`/v1/prk/${this.id}/catatan`)
        .then(res => {
          this.items = res.data.data
          console.log(this.items)
        })
        .finally(() => {})
    },
    createSave() {
      this.disabled = true
      let data = {
        catatan: this.create_data
      }
      this.$axios.post(`/v1/prk/${this.id}/catatan`, data)
        .then(res => {
          this.items.push(res.data.data)
          this.create_data = ''
        })
        .finally(() => {
          this.disabled = false
        })
    }
  },
  computed: {
    hasPermission() {
      return this.$auth.user.permissions.includes('edit-prk') || this.$auth.user.permissions.includes('superadmin')
    }
  }
}
</script>

<style>

</style>