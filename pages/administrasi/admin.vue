<template>
  <div class="container" v-if="hasPermission">
    <!-- snackbar start -->
    <v-snackbar v-model="snackbar">
      {{ snackbar_text }}
      <template v-slot:action="{ attrs }">
        <v-btn color="blue" text v-bind="attrs" @click="snackbar = false">
          TUTUP
        </v-btn>
      </template>
    </v-snackbar>
    <!-- snackbar end -->

    <!-- add admin dialog start -->
    <v-dialog v-model="add_admin_dialog" persistent max-width="400">
      <v-card class="rounded-lg" >
        <v-card-title class="text-h5">
          Tambah Admin Baru
        </v-card-title>
        <v-card-text>
          <v-text-field label="Nama" v-model="add_admin_data.nama" :error="add_admin_error.nama" dense outlined hide-details="auto" class="rounded-lg mb-3"></v-text-field>
          <v-text-field label="Email" v-model="add_admin_data.email"  :error="add_admin_error.email" dense outlined hide-details="auto" class="rounded-lg mb-3"></v-text-field>
          <v-text-field label="Password" v-model="add_admin_data.password" :error="add_admin_error.password" dense outlined hide-details="auto" class="rounded-lg mb-3"></v-text-field>
          <v-select label="Hak Akses" v-model="add_admin_data.permissions" :items="permission_options"  multiple chips outlined
            item-text="permission" item-value="permission" class="rounded-lg" hide-details="auto"></v-select>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="light" class="rounded-lg" text @click="addAdminClose">
            Batal
          </v-btn>
          <v-btn color="light-blue" class="rounded-lg" dark elevation="0" @click="addAdminSave">
            Simpan
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <!-- add admin dialog end -->

    <!-- update admin password dialog start -->
    <v-dialog v-model="update_admin_password_dialog" persistent max-width="400">
      <v-card class="rounded-lg" >
        <v-card-title class="text-h5">
          Update Password {{ update_admin_password_data.user.nama }}
        </v-card-title>
        <v-card-text>
          <v-text-field label="Password Baru" v-model="update_admin_password_data.password" :error="update_admin_password_error.password" dense outlined hide-details="auto" class="rounded-lg mb-3"></v-text-field>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="light" class="rounded-lg" text @click="updateAdminPasswordClose">
            Batal
          </v-btn>
          <v-btn color="light-blue" class="rounded-lg" dark elevation="0" @click="updateAdminPasswordSave">
            Simpan
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <!-- add admin password dialog end -->

    <!-- update permision dialog start -->
    <v-dialog v-model="edit_permission_dialog" persistent max-width="400">
      <v-card class="rounded-lg" >
        <v-card-title class="text-h5">
          Ubah Hak Akses "{{ edit_permission_data.nama }}"
        </v-card-title>
        <v-card-text>
          <v-select class="rounded-lg" v-model="permission_selected" :items="permission_options" label="Hak Akses" multiple chips outlined
            item-text="permission" item-value="permission" hide-details="auto"></v-select>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="light" class="rounded-lg" text @click="editPermissionClose">
            Batal
          </v-btn>
          <v-btn color="light-blue" class="rounded-lg" dark elevation="0" @click="editPermissionSave">
            Simpan
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <!-- update permision dialog end -->

    <!-- remove admin dialog start -->
    <v-dialog v-model="remove_admin_dialog" persistent max-width="400">
      <v-card class="rounded-lg">
        <v-card-title class="text-h5">
          Hapus Admin "{{ remove_admin_data.nama }}"
        </v-card-title>
        <v-card-text>
          Admin yang dihapus tidak dapat login ke dashboard lagi. Yakin hapus admin?
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="light" class="rounded-lg" text @click="closeRemoveAdminDialog">
            Batal
          </v-btn>
          <v-btn color="red" class="rounded-lg" dark elevation="0" @click="removeAdminSave">
            HAPUS
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <!-- remove admin dialog end -->

    <div class="d-flex justify-end">
      <v-breadcrumbs :items="breadcrumbItems"></v-breadcrumbs>
    </div>

    <div class="mb-5">
      <page-title title="Admin" description="List admin terdaftar" />
    </div>

    <div>
      <v-card elevation="4" class="rounded-lg">
        <v-card-title>
          <v-text-field style="max-width: 300px" class="rounded-lg" v-model="search" append-icon="mdi-magnify" label="Cari" single-line
            hide-details dense outlined></v-text-field>
          <v-spacer></v-spacer>
          <v-btn color="light-blue" class="rounded-lg" dark elevation="0" @click="add_admin_dialog = true">ADMIN BARU</v-btn>
        </v-card-title>
        <v-data-table :headers="headers" :items="admins" :loading="loading" class="elevation-1">
          <template v-slot:item.permissions="{ item }">
            <v-chip class="ma-2" v-for="(permission, index) in item.permissions" :key="index" small>
              {{ permission.permission.replace('-', ' ') }}
            </v-chip>
          </template>

          <template v-slot:item.menu="{ item }">
            <div class="d-flex justify-end">
              <v-btn dark color="light-blue" elevation="0" @click="openEditPermission(item)" small class="me-2 rounded-lg">Ubah Hak Akses
              </v-btn>
              <v-btn color="amber" elevation="0" @click="openUpdateAdminPasswordDialog(item)" small class="me-2 rounded-lg">Ubah Password
              </v-btn>
              <v-btn dark color="red" elevation="0" @click="openRemoveAdminDialog(item)" small class="rounded-lg">Hapus Admin
              </v-btn>
            </div>
          </template>
        </v-data-table>
      </v-card>
    </div>
  </div>
</template>
<script>
  import PageTitle from '~/components/PageTitle.vue'
  import BasketTab from '~/components/BasketTab.vue'
  export default {
    components: {
      PageTitle,
      BasketTab
    },
    data() {
      return {
        breadcrumbItems: [{
            text: 'Dashboard',
            disabled: false,
            href: '/',
          },
          {
            text: 'Administrasi',
            disabled: false,
            href: '/administrasi/profile',
          },
          {
            text: 'Admin',
            disabled: true,
            href: '/administrasi/admin',
          },
        ],

        search: '',
        loading: false,
        page: 1,
        admins: [],
        options: {},
        headers: [{
            text: 'Nama',
            align: 'start',
            value: 'nama',
          },
          {
            text: 'Email',
            value: 'email',
          },
          {
            text: 'Hak Akses',
            value: 'permissions',
          },
          {
            text: '',
            value: 'menu',
          },
        ],
        edit_permission_dialog: false,
        edit_permission_data: {},
        permission_options: [],
        permission_selected: [],

        remove_admin_dialog: false,
        remove_admin_data: {},

        add_admin_dialog: false,
        add_admin_data: {
          nama: '',
          email: '',
          password: '',
          permissions: []
        },
        add_admin_error: {
          nama: null,
          email: null,
          password: null
        },

        update_admin_password_dialog: false,
        update_admin_password_data: {
          user: {},
          password: ''
        },
        update_admin_password_error: {
          password: null
        },

        snackbar: false,
        snackbar_text: ''
      }
    },
    mounted() {
      this.getAdmin();
      this.getPermissionOptions()
    },
    methods: {
      getAdmin() {
        this.$axios.get(`/v1/admin?search=${this.search}&page=${this.page}`)
          .then(res => {
            this.admins = res.data.data
          })
      },
      getPermissionOptions() {
        this.$axios.get(`/v1/permission`)
          .then(res => {
            this.permission_options = res.data.data
          })
      },
      openEditPermission(user) {
        this.edit_permission_data = user;
        this.permission_selected = this.edit_permission_data.permissions.map(x => x.permission);
        this.edit_permission_dialog = true;
      },
      editPermissionSave() {
        let data = {
          user_id: this.edit_permission_data.id,
          permissions: this.permission_selected
        }

        this.$axios.post(`/v1/admin/${data.user_id}`, data)
          .then(res => {
            this.getAdmin();
            this.editPermissionClose()
          })
      },
      editPermissionClose() {
        this.edit_permission_data = {};
        this.permission_selected = [];
        this.edit_permission_dialog = false;
      },
      openRemoveAdminDialog(user) {
        this.remove_admin_data = user;
        this.remove_admin_dialog = true
      },
      closeRemoveAdminDialog() {
        this.remove_admin_data = {};
        this.remove_admin_dialog = false;
      },
      removeAdminSave() {
        this.$axios.delete(`/v1/admin/${this.remove_admin_data.id}`)
          .then(res => {
            this.remove_admin_data = {};
            this.remove_admin_dialog = false;
            this.getAdmin();
          })
      },
      addAdminClose() {
        this.add_admin_data = {
          nama: '',
          email: '',
          password: '',
          permissions: []
        };
        this.add_admin_dialog = false;
      },
      addAdminSave() {
        this.add_admin_error = {
          nama: null,
          email: null,
          password: null
        }
        this.$axios.post(`/v1/admin`, this.add_admin_data)
          .then(res => {
            this.add_admin_data = {
              nama: '',
              email: '',
              password: '',
              permissions: []
            };
            this.getAdmin();
            this.add_admin_dialog = false;

            this.snackbar_text = 'Admin baru berhasil ditambahkan.';
            this.snackbar = true;
          })
          .catch(e => {
            this.add_admin_error.nama = e.response.data.nama;
            this.add_admin_error.email = e.response.data.email;
            this.add_admin_error.password = e.response.data.password;
            if(e.response.data.message) {
              this.snackbar_text = e.response.data.message;
              this.snackbar = true;
            }
          })
      },
      openUpdateAdminPasswordDialog(user) {
        this.update_admin_password_data.user = JSON.parse(JSON.stringify(user));
        this.update_admin_password_dialog = true;
      },
      updateAdminPasswordClose() {
        this.update_admin_password_dialog = false;
        this.update_admin_password_data = {
          user: {},
          password: ''
        };
        this.update_admin_password_error = {
          password: null
        };
      },
      updateAdminPasswordSave() {
        this.update_admin_password_error = {
          password: null
        }
        this.$axios.post(`/v1/admin/${this.update_admin_password_data.user.id}/password`, this.update_admin_password_data)
          .then(res => {
            this.getAdmin();
            this.updateAdminPasswordClose();

            this.snackbar_text = 'Password Admin berhasil diubah.';
            this.snackbar = true;
          })
          .catch(e => {
            this.add_admin_error.password = e.response.data.password;
            if(e.response.data.message) {
              this.snackbar_text = e.response.data.message;
              this.snackbar = true;
            }
          })
      },
    },
    computed: {
      hasPermission() {
        return this.$auth.user.permissions.includes('superadmin');
      }
    }
  }
</script>

<style>

</style>