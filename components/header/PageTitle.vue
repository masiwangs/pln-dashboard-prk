<template>
  <div class="mb-5">
    <p class="mb-1">
      <v-chip color="light-blue" dark>
        <span class="white--text">
          BASKET {{ item.basket }}
        </span>
      </v-chip>
    </p>
    <div>
      <h3>{{ item.title ? item.title : '{page_title}' }}</h3>
      <span>{{ item.description ? item.description : '{page_description}' }}</span>
    </div>
  </div>
</template>

<script>
export default {
  props: ['item']
}
</script>

<style>

</style>