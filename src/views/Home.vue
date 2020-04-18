<template>
  <div class="container-fluid">
  <nav class="navbar navbar-light bg-light">
  <a class="navbar-brand" href="#">
    rafiki
  </a>
</nav>
      <div align="center">
         <div class="card" style="width: 20rem;" v-if="loginbutton == true">
           <h4 class="card-header">Login</h4>
            <div class="card-body">
              <input type="email" v-model="email" style="width: 15rem;" class="form-control form-control-sm" placeholder="Email"><br>
               <input type="password" v-model="password" style="width: 15rem;" class="form-control form-control-sm" placeholder="Password">
              <br>
              <div class="alert" role="alert" v-if="info !== ''">
              {{info}}
              </div>
              <button class="btn btn-outline-dark btn-block" style="width: 15rem;" @click="login">{{loginbtn}}</button> 
          </div>
          <a @click="loginbutton = false" class="blockquote-footer">Sign Up</a>
        </div>

        <div v-else>
          <div class="card" style="width: 20rem;">
            <h4 class="card-header">Signup</h4>
            <div class="card-body">
              <input type="email" v-model="email" style="width: 15rem;" class="form-control form-control-sm" placeholder="Email"> 
              <br>
              <div class="form-row">
                <div class="col">
                  <input type="text" v-model="firstname" class="form-control form-control-sm" placeholder="First name">
                </div>
                <div class="col">
                  <input type="text" v-model="lastname"  class="form-control form-control-sm" placeholder="Last name">
                </div>
             </div> <br>
             <input type="password" v-model="password" class="form-control form-control-sm" placeholder="Password"><br>

             <input type="text" v-model="industry" class="form-control form-control-sm" placeholder="Industry"><br>
             <div class="alert" role="alert" v-if="info !== ''">
              {{info}}
              </div>
             <button class="btn btn-outline-dark btn-block" @click="signup">{{signupbtn}}</button> 
            </div>
            <a @click="loginbutton = true" class="blockquote-footer">Login</a>
          </div>
          
        </div>
      </div>

      <div class="jumbotron"></div>
     
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'Home',
  data() {
    return{
      loginbutton: true,
      email: '',
      password: '',
      firstname: '',
      lastname: '',
      industry: '',
      info: '',
      loginbtn: 'Login',
      signupbtn: 'Signup'
    }
  },
  methods: {
    signup(){
      this.signupbtn = 'Give me a sec...'
      var email = this.email.toLowerCase()
      var firstname = this.firstname
      var lastname = this.lastname
      var password = this.password
      var industry = this.industry
      axios.post('http://localhost:3000/api/createaccount', {
        email: email,
        firstname: firstname,
        lastname: lastname,
        password: password,
        industry: industry
      }).then( res => {
        console.log(res.data)
        if (res.data.message == "User's email exist") {
          this.info = 'Email has been registered by another user, Please login'
          this.signupbtn = 'Signup' 
        } else {
          sessionStorage.setItem('firstname', this.firstname)
          sessionStorage.setItem('id', res.data._id)
          sessionStorage.setItem('alert', true)
          this.$router.push('/dashboard')
        }
      }).catch(err => {
        this.info = err
        this.signupbtn = 'Signup'
      })
    },
    login() {
      this.loginbtn = 'Give me a sec...'
      var email = this.email.toLowerCase()
      var password = this.password
      if(email == '' || password == '') {
        this.info = 'Fill your email & password'
        this.loginbtn = 'Please login'
      } else {
        axios.post('http://localhost:3000/api/login', {
        email: email,
        password: password
      }).then(res => {
        console.log(res.data)
        if (res.data.length == 1){
          sessionStorage.setItem('firstname', res.data[0].firstname)
          sessionStorage.setItem('id', res.data[0]._id)
          sessionStorage.setItem('alert', false)
          this.$router.push('/dashboard')
        } else {
          this.info = 'email or password incorrect'
          this.loginbtn = 'Login'
        }
      }).catch(err => {
        this.info = err
        this.loginbtn = 'Login'
      })
      }
    }
  }
}
</script>
<style lang="css" scoped>
  .navbar {
    background: transparent!important;
}
.navbar-brand{
  color: white !important;
}
.container-fluid {
  background-image: linear-gradient(45deg, rgb(20, 20, 20) 50%, rgb(255, 255, 255) 50%);
  height: 100%;
}
.card-header {
  background: rgb(26, 26, 29) ;
  color: white;
}

 .jumbotron {
  background: transparent!important;
  outline: none;
  border-color: none;
}
.jumbotron {
    height: 18rem;
}
</style>