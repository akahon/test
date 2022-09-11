<template>
  <v-app id="inspire">
    <v-content>
      <v-container fluid fill-height>
        <v-layout align-center justify-center>
          <v-flex xs12 sm8 md4>
            <v-card class="elevation-12">
              <v-toolbar dark color="primary">
                <v-toolbar-title>Login form</v-toolbar-title>
              </v-toolbar>
              <v-card-text>
                <v-form>
                  <v-text-field
                    v-model="login"
                    name="login"
                    label="Username-token"
                    type="text"
                    :maxlength="16"
                    @focus="error = false"
                  />
                </v-form>
              </v-card-text>
              <v-card-actions>
                <v-spacer />
                <v-btn color="primary" :loading="loading" :disabled="login.length !== 16" @click="onSubmit">Login</v-btn>
              </v-card-actions>
            </v-card>
            <v-snackbar :timeout="3000" :value="error" absolute top tile color="red accent-2">
              The 'Username-token' should not allow any special characters, nor numbers or cyrillic symbols and 16-character length.
            </v-snackbar>
          </v-flex>
        </v-layout>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
export default {
  name: 'TheLogin',
  props: {},
  data () {
    return {
      login: '',
      error: false,
      loading: false,
    }
  },

  beforeMount () {
    sessionStorage.getItem('token') && this.$router.push('/')
  },

  methods: {
    onSubmit () {
      this.loading = true
      const regex = /^[a-zA-Z]{16}$/
      const isCorrect = this.login.match(regex)
      setTimeout(() => {
        if (isCorrect) {
          this.loading = false
          sessionStorage.setItem('token', this.login)
          this.$router.push('/')
        } else {
          this.loading = false
          this.error = true
        }
      }, 3000)
    }
  }
}
</script>
