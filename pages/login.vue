<template>
  <!-- <div v-if="state == 'Loggedin'">
    <h1>Hello</h1>
  </div>
  <div v-else>
    <LoginPage />
  </div> -->
  <LoginPage />
</template>

<script>
import LoginPage from '../components/loginPage.vue'
export default {
  name: 'Login',
  layout: 'unauth',
  components: {
    LoginPage
  }
}
</script>
