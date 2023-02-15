<template>
  <div class="container">
    <NavBar style="width:100%;"/>
    <div class="user_details mt-3">
        <b-card no-body class="overflow-hidden p-3" style="width: 100%;">
          <!-- <pre>{{$store.state.auth.user}}</pre> -->
            <b-row no-gutters>
            <b-col md="6">
                <b-card-img style="width: 50%;" :src="$store.state.auth.user.role != 'SUPER' ? 'https://cdn-icons-png.flaticon.com/512/3135/3135715.png' : 'https://cdn-icons.flaticon.com/png/512/3590/premium/3590466.png?token=exp=1652860313~hmac=188e5ffc82507eb2110c5fcfd0b06bff'" alt="Image" class="rounded-0"></b-card-img>
            </b-col>
            <b-col md="6">
                <b-card-body :title="$store.state.auth.user.name">
                    <b-card-text>
                        {{$store.state.auth.user.email}}
                    </b-card-text>
                    <!-- <pre>{{$store.state.auth}}</pre> -->
                    <b-card-text v-if="$store.state.auth.user.modules">
                        Modules: {{$store.state.auth.user.modules.length}}
                    </b-card-text>
                </b-card-body>
            </b-col>
            </b-row>
        </b-card>
    </div>
  </div>
</template>

<script>
import NavBar from '../components/navBar.vue'
export default {
  name: 'userDetails',
  middleware: 'auth',
  components: {
    NavBar,
  },
  async created() {
    if (!this.$auth.loggedIn || !this.$store.state.auth.user.id){
      this.$router.push('/login')
    }
  }, 
  methods: {
    
  }
}
</script>

<style scoped>
@import url('https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css');
.container{
  padding: 0%;
}
</style>
