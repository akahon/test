<template>
  <div>
    <v-card-title>
      <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        label="Search"
        single-line hide-details
        @input="onSearch"
      />
    </v-card-title>

    <v-data-table
      class="elevation-1 mt-10"
      :headers="headers"
      :items="books"
      :server-items-length="count"
      :loading="loading"
      :disable-filtering="true"
      :disable-sort="true"
      loading-text="Loading... Please wait"
      @pagination="toPage"
      :footer-props="{
        itemsPerPageOptions: [5, 10, 15, 20]
      }"
    >
      <template #[`item.volumeInfo.imageLinks.thumbnail`]="{ item }">
        <v-img :src="item.volumeInfo.imageLinks.thumbnail" max-width="100" max-height="100" class="my-2" />
      </template>
      <template #[`item.volumeInfo.authors`]="{ item }">
        <v-list>
          <v-list-item v-for="i in item.volumeInfo.authors" :key="i">
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
  async asyncData ({ $axios }) {
    const books = await $axios.$get(`/volumes?q=programming`)
    return { books: books.items, count: books.totalItems }
  },
  data () {
    return {
      count: 0,
      timeout: null,
      loading: false,
      search: '',
      headers: [
        {
          text: 'Thumbnail',
          align: 'start',
          sortable: false,
          value: 'volumeInfo.imageLinks.thumbnail'
        },
        { text: 'Title ', value: 'volumeInfo.title' },
        { text: 'Author', value: 'volumeInfo.authors' },
      ],
      query: {
        q: 'programming',
        startIndex: 1,
        maxResults: 10
      }
    }
  },

  beforeMount() {
    if (!sessionStorage.getItem('token')) {
      this.$router.push('/login')
    }
  },

  methods: {
    onSearch (text) {
      this.query.q = text || 'programming'
      clearTimeout(this.timeout)
      this.loading = true
      this.timeout = setTimeout(() => {
        this.fetchBooks()
        this.loading = false
      }, 500)
    },

    async fetchBooks () {
      const data = await this.$axios.$get(`/volumes`, { params: this.query })
      this.books = data.items
      this.count = data.totalItems
    },

    toPage (pagination) {
      this.query.maxResults = pagination.itemsPerPage
      this.query.startIndex = pagination.pageStart
      this.fetchBooks()
    }
  }
}
</script>

<style>
.theme--dark.v-list {
  background: none !important;
}
</style>
