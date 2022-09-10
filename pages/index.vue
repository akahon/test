<template>
  <div>
    <v-card-title>
      <v-text-field v-model="search" append-icon="mdi-magnify" label="Search" single-line hide-details></v-text-field>
    </v-card-title>
    <v-data-table :headers="headers" :items="books" :items-per-page="5" class="elevation-1 mt-10">
      <!-- eslint-disable-next-line vue/valid-v-slot -->
      <template #item.volumeInfo.authors="{ item }">
        <v-list :dark="false">
          <v-list-item :dark="true" v-for="i in item.volumeInfo.authors" :key="i">
            <v-list-item-content>
            <v-list-item-title>
              {{ i }}
            </v-list-item-title>
          </v-list-item-content>
          </v-list-item>
        </v-list>
      </template>
    </v-data-table>
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
  async asyncData ({ $axios, route }) {
    const books = await $axios.$get(`/volumes`, {
      params: {
        q: route.params,
        per_page: 200,
      },
    })
    console.log("🚀 ~ file: index.vue ~ line 31 ~ asyncData ~ books", books.items)
    return { books: books.items }
  },
  data () {
    return {
      // books: [],
      search: '',
      headers: [
        {
          text: 'Thumbnail',
          align: 'start',
          sortable: false,
          value: 'id'
        },
        { text: 'title ', value: 'volumeInfo.title' },
        { text: 'author', value: 'volumeInfo.authors' },
      ]
    }
  }
}
</script>

<style>
  .theme--dark.v-sheet {
    background-color: none !important;
  }
  .theme--dark.v-list {
    background-color: none !important;
  }
</style>
