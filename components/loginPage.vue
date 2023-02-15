<template>
  <div>
    <div v-if="state == 'login'">
      <div class="form">
        <h3>Login Form</h3>
        <b-form-input class="inp"
          type="text"
          placeholder="Email"
          v-model="lemail"
          ></b-form-input>
        <b-form-input id="pass" class="inp"
          type="password"
          placeholder="Password"
          v-model="lpassword"
          ></b-form-input>
        <b-btn variant="outline-dark" @click="showPassword"><i :class="showPass?'fa fa-eye':'fa fa-eye-slash'" aria-hidden="true"></i></b-btn>
        <b-btn variant="outline-success" @click="login">Login</b-btn>
        <br><p>Not registered? <b-link class="link" @click="goRegister">click here.</b-link></p>
      </div>
    </div>
    <div v-else>
      <div class="form">
        <h3>Registration Form</h3>
        <b-form-input class="inp"
          type="text"
          placeholder="Full name"
          v-model="rname"
          ></b-form-input>
        <b-form-input class="inp"
          type="text"
          placeholder="Email"
          v-model="remail"
          ></b-form-input>
        <b-form-input id="pass" class="inp"
          type="password"
          placeholder="Password"
          v-model="rpass"
          ></b-form-input>
        <b-form-input id="cpass" class="inp"
          type="password"
          placeholder="Confirm Password"
          v-model="rcpass"
          ></b-form-input>
        <b-btn variant="outline-dark" @click="showPassword"><i :class="showPass?'fa fa-eye':'fa fa-eye-slash'" aria-hidden="true"></i></b-btn>
        <b-btn variant="outline-success" @click="register">Register</b-btn>
        <br><p>Already registered? <b-link class="link" @click="goLogin">click here.</b-link></p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'LoginPage',
  data() {
    return {
      state: 'login',
      showPass: false,
      lemail: 'debanjan@deb.com',
      lpassword: '1234',
      remail: '',
      rname: '',
      rpass: '',
      rcpass: ''
    }
  },
  created() {
    if (this.$store.state.auth.loggedIn && this.$store.state.auth.user) {
      this.$router.push({path: '/'})
    }
  },
  methods: {
    async login() {
      try {
        const response = await this.$auth.loginWith("local", {
          data: { email: this.lemail, password: this.lpassword },
        });
        // await this.$auth.setUserToken(`Bearer ${response.data.data.AccessToken}`)
        // const resp = await this.$axios.get("user/get-details")
        // console.log(resp);
        // await this.$auth.setUser(resp.data.data.user)
        if (await this.$auth.loggedIn){
          this.$router.push('/')
        }
      } catch (error) {
        this.$bvToast.toast( 'You are unauthorized. Try registering yourself.' , {
          variant: 'danger',
          solid: true
        })
      }
    },
    showPassword() {
      this.showPass = !this.showPass
      document.getElementById('pass').setAttribute('type', this.showPass?"text":"password")
      if (document.getElementById('cpass') != null){
        document.getElementById('cpass').setAttribute('type', this.showPass?"text":"password")
      }
    },
    goRegister() {
      this.setDefault()
      this.state = 'register'
    },
    goLogin() {
      this.setDefault()
      this.state = 'login'
    },
    setDefault() {
      this.showPass= false,
      this.lemail= '',
      this.lpassword= '',
      this.remail= '',
      this.rname= '',
      this.rpass= '',
      this.rcpass= ''
    }
  }
}
</script>


<style scoped>
@import url("https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css");
.form{
  width: 70%;
  text-align: center;
  margin-top: 20%;
  margin-left: 50%;
  transform: translate(-50%, -20%);
  border: 1px solid black;
  padding: 50px;
}
.inp {
  margin: 5px;
}
.link{
  font-size: 1em;
}
.link:hover{
  font-size: 1.1em;
}
</style>