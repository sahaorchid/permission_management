<template>
  <div>
    <b-navbar type="light" variant="light">
      <b-navbar-nav>
        <b-nav-item :href="this.$store.state.auth.user.role == 'SUPER' ? '/' : '/indexForUser'">Home</b-nav-item>

        <b-nav-item-dropdown  :text="$store.state.auth.user.name.includes(' ')? `${$store.state.auth.user.name.split(' ')[0].charAt(0).toUpperCase()+ ' ' + $store.state.auth.user.name.split(' ')[1].charAt(0).toUpperCase()}` : $store.state.auth.user.name" :left='true'>
          <b-dropdown-item class="name" @click.prevent="showDetails">{{$store.state.auth.user.name}}</b-dropdown-item>
          <b-dropdown-item @click="logOut">Logout</b-dropdown-item>
        </b-nav-item-dropdown>
      </b-navbar-nav>
    </b-navbar>
  </div>
</template>

<script>
export default {
  name: "navBar",
  middleware: "auth",
  created() {
    if (!this.$store.state.auth.loggedIn && !this.$store.state.auth.user) {
      this.$router.push({path: '/login'})
    }
  },
  methods: {
    async logOut() {
      await this.$auth.logout();
      this.$bvToast.toast( 'Successfully logged out.' , {
        variant: 'danger',
        solid: true
      })
      this.$router.push("/login");
    },
    showDetails() {
      this.$router.push({path: '/user_details'})
    }
  },
};
</script>


<style scoped>
.name {
  text-transform: capitalize;
  font-size: 1.8em;
}
</style>
