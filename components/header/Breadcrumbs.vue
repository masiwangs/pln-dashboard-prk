<template>
  <div class="d-flex justify-end">
    <v-breadcrumbs :items="items[0] ? items : placeholder"></v-breadcrumbs>
  </div>
</template>

<script>
  export default {
    props: ['items'],
    data() {
      return {
        placeholder: [
          {
            text: '{breadcrumbs}'
          }
        ]
      }
    }
  }
</script>

<style>

</style>