<template>
  <div>
    <v-dialog v-model="dialog" persistent max-width="400">
      <v-card class="rounded-lg">
        <v-card-title class="text-h5">
          {{ dialogTitle }}
        </v-card-title>
        <v-card-text>
          <v-file-input 
            v-model="file" 
            color="blue"
            label="File"
            placeholder="Select your files" 
            hide-details="auto"
            class="rounded-lg mb-3"
            dense
            outlined 
            :show-size="1000"
            accept=".xlsx"
          >
          </v-file-input>
          <small>Unduh template <a :href="templateUrl">disini.</a></small>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="light" class="rounded-lg" text @click="cancel">
            BATAL
          </v-btn>
          <v-btn color="blue" class="rounded-lg" dark elevation="0" @click="upload">
            IMPORT
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
export default {
    props: ['dialog', 'dialog-title', 'template-url', 'upload-url'],
    data() {
        return {
            file: null
        }
    },
    methods: {
        upload() {
            let data = new FormData();
            data.append(`upload`, this.file)
            this.$axios.post(this.uploadUrl, data, {
                'Content-Type': 'multipart/form-data'
            })
            .then(res => {
                this.file = null;
                this.$emit('done');
            })
            .catch(e => {
                this.$emit('error', e.response.data)
            })
        },
        cancel() {
            this.file = null;
            this.$emit('cancel');
        }
    }
}
</script>

<style>

</style>