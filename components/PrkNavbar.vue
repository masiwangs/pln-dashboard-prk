<template>
<v-card
  elevation="4"
  class="rounded-lg mb-5"
>
  <v-tabs 
    hide-slider 
    background-color="light-blue" 
    dark
    color="yellow"
    show-arrows
    v-model="active_tab"
  >
    <v-tab 
      v-for="tab in tabs"
      nuxt 
      exact 
      :key="tab.id" 
      :to="`/tahapan/prk/${basket}/${id}/${tab.slug}`">
      {{ tab.title }}
    </v-tab>
  </v-tabs>
</v-card>
</template>

<script>
  export default {
    props: ['active', 'prk-id', 'has-permission'],
    created() {
      this.basket = this.$route.params.basket; 
      this.id = this.$route.params.id; 
    },
    mounted() {
      this.active_tab = this.active
    },
    data() {
      return {
        active_tab: '',
        id: ''
      }
    },
    computed: {
      tabs() {
        let tabs = [
          {
            id: 'informasi', 
            slug: '',
            title: 'INFORMASI PROJECT'
          },
          {
            id: 'rab-jasa', 
            slug: 'rab-jasa',
            title: 'RAB JASA'
          },
          {
            id: 'rab-material', 
            slug: 'rab-material',
            title: 'RAB MATERIAL'
          },
          {
            id: 'lampiran', 
            slug: 'lampiran',
            title: 'LAMPIRAN BERKAS'
          },
          {
            id: 'catatan', 
            slug: 'catatan',
            title: 'CATATAN'
          },
        ];

        if(this.hasPermission) {
          tabs.push({
            id: 'hapus', 
            slug: 'hapus',
            title: 'HAPUS'
          });
        }

        return tabs;
      }
    }
  }
</script>

<style>

</style>