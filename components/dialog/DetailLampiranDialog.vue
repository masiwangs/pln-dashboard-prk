<template>
  <v-dialog v-model="dialog" persistent max-width="400">
    <v-card>
      <v-card-title class="text-h5">
        <img :src="file.thumbnail" alt="" srcset="" style="height: 1.5rem; mergin-right: 5px">
        <span style="font-size: 1rem">{{ file ? file.nama : '' }}</span>
      </v-card-title>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="light" text @click="cancel">
          BATAL
        </v-btn>
        <v-btn color="red" text @click="destroy">
          HAPUS
        </v-btn>
        <v-btn color="blue" text @click="download">
          SIMPAN
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  props:['dialog', 'tahap', 'file'],
  methods: {
    download() {
      window.open(this.file.uri);
    },
    destroy() {
      this.$axios.delete(`/${this.tahap}/${this.$route.params.id}/lampiran/${this.file.id}`)
        .then(res => {
          this.$emit('close')
          this.$emit('reload')
        })
    },
    cancel() {
      this.$emit('close')
    }
  }
}
</script>

<style>

</style>