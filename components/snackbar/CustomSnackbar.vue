<template>
  <v-snackbar 
    v-model="snackbar"
    :timeout="timeout"
  >
    {{ text }}

    <template v-slot:action="{ attrs }">
      <v-btn color="blue" text v-bind="attrs" @click="$emit('close')">
        &times;
      </v-btn>
    </template>
  </v-snackbar>
</template>

<script>
  export default {
    props: ['snackbar', 'text', 'timeout']
  }
</script>

<style>

</style>