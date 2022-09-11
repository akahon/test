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
      :options.sync="pagination"
      :footer-props="{
        pagination: pagination,
        itemsPerPageOptions: [5, 10, 15, 20]
      }"
      @pagination="toPage"
    >
      <template #[`item.volumeInfo.imageLinks.thumbnail`]="{ item }">
        <v-img
          v-if="item.volumeInfo && item.volumeInfo.imageLinks && item.volumeInfo.imageLinks.thumbnail"
          :src="item.volumeInfo.imageLinks.thumbnail"
          max-width="100" max-height="100"
          class="my-2"
        />
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
      },
      pagination: {
          page: 1,
          itemsPerPage: 10,
          pageStart: 0,
          pageStop: 10,
          pageCount: 10,
          itemsLength: 10
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
      this.pagination.page = 1
      this.pagination.pageStart = 1
      this.query.startIndex = 1
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
      this.pagination = pagination
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
