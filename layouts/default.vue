<template>
  <v-app dark>
    <v-navigation-drawer
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      fixed
      app
      style="background-color: #fafafa"
    >
      <v-list
        dense
        rounded
      >
        <v-list-item v-if="drawer">
          <v-list-item-content>
            <h2 class="">MONITORING</h2>
          </v-list-item-content>
        </v-list-item>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to ? item.to : ''"
          :router="item.to"
          :class="{'blue--text': item.to, 'v-list-item--active': isActive(item)}"
        >
          <v-list-item-action v-if="item.icon">
            <v-icon color="blue">{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title color="blue" v-text="item.title" />
          </v-list-item-content>
        </v-list-item>
        <v-list-item
          @click="logout"
          class="red--text"
        >
          <v-list-item-action>
            <v-icon color="red">mdi-power</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title v-text="'Keluar'" />
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar
      :clipped-left="clipped"
      fixed
      app
      elevation="0"
      style="background-color: #fafafa"
    >
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      <v-toolbar-title v-if="!drawer">
        <h3>MONITORING</h3>
      </v-toolbar-title>
      <v-spacer />
    </v-app-bar>
    <v-main style="background-color: #fafafa">
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: 'DefaultLayout',
  data () {
    return {
      clipped: false,
      drawer: true,
      fixed: false,
      
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Vuetify.js'
    }
  },
  methods: {
    async logout() {
      await this.$auth.logout()
      this.$router.push('/login');
    },
    isActive(item) {
      if(this.$route.path.match(new RegExp(`${item.path_regex}(.*)`, 'gi')) && item.to.match(new RegExp(`${item.path_regex}(.*)`, 'gi'))) {
        return true;
      }
    }
  },
  computed: {
    hasPermission() {
      return this.$auth.user.permissions.includes('superadmin');
    },
    items () {
      let items = [
        {
          icon: 'mdi-apps',
          title: 'Dashboard',
          to: '/'
        },
        {
          icon: '',
          title: 'Tahapan',
          to: ''
        },
        {
          icon: 'mdi-puzzle',
          title: 'PRK',
          to: '/tahapan/prk/1',
          path_regex: 'tahapan/prk'
        },
        {
          icon: 'mdi-view-grid',
          title: 'SKKI',
          to: '/tahapan/skki/1',
          path_regex: 'tahapan/skki'
        },
        {
          icon: 'mdi-cart',
          title: 'Pengadaan',
          to: '/tahapan/pengadaan/1',
          path_regex: 'tahapan/pengadaan'
        },
        {
          icon: 'mdi-file-document',
          title: 'Kontrak',
          to: '/tahapan/kontrak/1',
          path_regex: 'tahapan/kontrak'
        },
        {
          icon: 'mdi-crane',
          title: 'Pelaksanaan',
          to: '/tahapan/pelaksanaan/1',
          path_regex: 'tahapan/pelaksanaan'
        },
        {
          icon: 'mdi-cash-multiple',
          title: 'Pembayaran',
          to: '/tahapan/pembayaran/1',
          path_regex: 'tahapan/pembayaran'
        },
        {
          icon: '',
          title: 'Database',
          to: ''
        },
        {
          icon: 'mdi-cable-data',
          title: 'Material',
          to: '/database/material'
        },
        {
          icon: '',
          title: 'Administrasi',
          to: ''
        },
        {
          icon: 'mdi-account',
          title: 'Profil Anda',
          to: '/administrasi/profile'
        },
      ];

      if(this.hasPermission) {
        items = [
          {
            icon: 'mdi-apps',
            title: 'Dashboard',
            to: '/'
          },
          {
            icon: '',
            title: 'Tahapan',
            to: ''
          },
          {
            icon: 'mdi-puzzle',
            title: 'PRK',
            to: '/tahapan/prk/1',
            path_regex: 'tahapan/prk'
          },
          {
            icon: 'mdi-view-grid',
            title: 'SKKI',
            to: '/tahapan/skki/1',
            path_regex: 'tahapan/skki'
          },
          {
            icon: 'mdi-cart',
            title: 'Pengadaan',
            to: '/tahapan/pengadaan/1',
            path_regex: 'tahapan/pengadaan'
          },
          {
            icon: 'mdi-file-document',
            title: 'Kontrak',
            to: '/tahapan/kontrak/1',
            path_regex: 'tahapan/kontrak'
          },
          {
            icon: 'mdi-crane',
            title: 'Pelaksanaan',
            to: '/tahapan/pelaksanaan/1',
            path_regex: 'tahapan/pelaksanaan'
          },
          {
            icon: 'mdi-cash-multiple',
            title: 'Pembayaran',
            to: '/tahapan/pembayaran/1',
            path_regex: 'tahapan/pembayaran'
          },
          {
            icon: '',
            title: 'Database',
            to: ''
          },
          {
            icon: 'mdi-cable-data',
            title: 'Material',
            to: '/database/material'
          },
          {
            icon: '',
            title: 'Administrasi',
            to: ''
          },
          {
            icon: 'mdi-account-tie',
            title: 'Admin',
            to: '/administrasi/admin'
          },
          {
            icon: 'mdi-account',
            title: 'Profil Anda',
            to: '/administrasi/profile'
          },
        ]
      }

      return items;
    }
  }
}
</script>

<style>
  .v-application {
    font-family: 'Rubik', sans-serif!important;
  }
  h1, h2, h3, h4, h5, h6, p, span {
    color: #333
  }
</style>