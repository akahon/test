<template>
  <div>
    <v-card-title>
      <v-text-field v-model="search" append-icon="mdi-magnify" label="Search" single-line hide-details></v-text-field>
    </v-card-title>
    <v-data-table :headers="headers" :items="desserts" :items-per-page="5" class="elevation-1 mt-10" />
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
}
</script>
