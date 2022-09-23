<template>
  <div>
    <v-btn v-if="hasPermission" elevation="0" class="rounded-lg" dark color="light-blue" @click="dialog = true">BARU</v-btn>
    <v-dialog v-model="dialog" persistent max-width="400">
      <v-card
        class="rounded-lg"
      >
        <v-card-title class="text-h5">
          Upload Lampiran
        </v-card-title>
        <v-card-text>
          <v-file-input v-model="files" 
            color="blue" 
            counter 
            label="Lampiran" 
            multiple
            placeholder="Select your files" 
            hide-details="auto"
            class="rounded-lg"
            dense
            outlined :show-size="1000">
            <template v-slot:selection="{ index, text }">
              <v-chip v-if="index < 2" color="blue" dark label small>
                {{ text }}
              </v-chip>

              <span v-else-if="index === 2" class="text-overline grey--text text--darken-3 mx-2">
                +{{ files.length - 2 }} File(s)
              </span>
            </template>
          </v-file-input>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="light" text @click="cancel">
            Batal
          </v-btn>
          <v-btn color="light-blue" dark class="rounded-lg" elevation="0" @click="upload">
            SIMPAN
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
export default {
  props: ['tahap', 'has-permission'],
  data() {
      return {
        dialog: false,
        files: []
      }
    },
    methods: {
      upload() {
        let data = new FormData();
        this.files.forEach((file, index) => {
          data.append('lampirans['+index+']', file)
        });

        this.$axios.post(`/v1/${this.tahap}/${this.$route.params.id}/lampiran`, data, {
            'Accept': 'application/json, text/plain, */*',
            'Content-Type': 'multipart/form-data'
          })
          .then(res => {
            this.files  = [];
            this.dialog = false;
            this.$emit('reload');
          })
      },
      cancel() {
        this.files  = [];
        this.dialog = false;
      }
    }
}
</script>

<style>

</style>