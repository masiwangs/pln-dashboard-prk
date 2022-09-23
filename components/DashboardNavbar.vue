<template>
  <v-card
    class="rounded-lg"
    elevation="4"
  >
    <v-tabs 
      hide-slider 
      background-color="light-blue" 
      dark
      color="yellow"
      show-arrows
      v-model="active_tab"
      class="rounded-lg"
    >
      <v-tab 
        v-for="tab in tabs"
        nuxt 
        exact 
        :key="tab.id" 
        :to="`/${tab.slug}`">
        {{ tab.title }}
      </v-tab>
    </v-tabs>
  </v-card>
</template>

<script>
  export default {
    props: ['active'],
    mounted() {
      this.active_tab = this.active
    },
    data() {
      return {
        active_tab: '',
        id: '',
        tabs : [
          {
            id: 'semua', 
            slug: '',
            title: 'SEMUA BASKET'
          },
          {
            id: '1', 
            slug: 'basket/1',
            title: 'BASKET 1'
          },
          {
            id: '2',
            slug: 'basket/2', 
            title: 'BASKET 2'
          },
          {
            id: '3',
            slug: 'basket/3',
            title: 'BASKET 3'
          },
        ]
      }
    }
  }
</script>

<style>

</style>