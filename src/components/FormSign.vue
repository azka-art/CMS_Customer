<template>
  <div class="signForm">
      <h1 id="headlineSign">{{headline}}</h1>
      <div class="innerForm d-flex justify-content-center align-items-center">
          <form>
              <input class="form-control" type="text" name="email" id="email" placeholder="Enter your email" v-model="email"><br>
              <input class="form-control" type="password" name="password" id="password" placeholder="Enter your password" v-model="password"><br>
              <button @click.prevent="login" class="btn btn-outline-light form-control" v-if="signIn == 'true' && !isLoading">Sign In</button>
              <button @click.prevent="login" class="btn btn-outline-light form-control" v-if="isLoading"><img src="../assets/Ellipsis-1s-22px.gif" alt=""></button>
              <button @click.prevent="signUp" class="btn btn-outline-light form-control" v-if="signIn == 'false' && !isLoading">Sign Up</button>
          </form>
      </div>
  </div>
</template>

<script>
import axios from 'axios'
import { mapMutations } from 'vuex'
export default {
  methods: {
    ...mapMutations(['SET_ISLOGIN']),
    login () {
      this.isLoading = true
      axios({
        method: 'POST',
        url: this.$store.state.baseUrl + '/users/login',
        data: {
          email: this.email,
          password: this.password
        }
      })
        .then((result) => {
          localStorage.access_token = result.data.token
          this.SET_ISLOGIN(true)
          this.email = ''
          this.password = ''
          this.isLoading = false
          this.$router.push('/')
        }).catch((err) => {
          console.log(err.response.data)
          this.$swal.fire({
            icon: 'error',
            title: 'Oops...',
            text: `${err.response.data.errors[0].message}`,
          })
        })
        .finally(() => {
          this.isLoading = false
        })
    },
    signUp () {
      axios({
        method: 'POST',
        url: this.$store.state.baseUrl + '/users/register',
        data: {
          email: this.email,
          password: this.password
        }
      })
        .then((result) => {
          this.$swal.fire({
            position: 'center',
            icon: 'success',
            text: `${this.email} has been registered, please sign in before shopping`,
            showConfirmButton: false,
            timer: 1500
          })
          console.log(result)
          this.$router.push('/signIn')
          this.email = ''
          this.password = ''
        }).catch((err) => {
          console.log(err)
        })
    }
  },
  data () {
    return {
      password: '',
      email: '',
      isLoading: false
    }
  },
  name: 'SignForm',
  props: ['signIn', 'headline']
}
</script>

<style scoped>
.innerForm {
    margin-top: 40px;
    border-radius: 15px;
    width: 390px;
    height: 250px;
    background-color: #CFC08A;
}
.btn {
  color: #CFC08A;
  background-color: white;
  font-family: 'Baloo Paaji 2', cursive ! important;
}

.btn:hover {
  color: white;
  background-color: #CFC08A;
}

#headlineSign {
  font-family: 'Elsie', cursive;
  color: #CFC08A;
}
</style>
