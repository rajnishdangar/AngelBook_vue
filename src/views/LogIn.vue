<template>
  <div class="page-Log-in">
    <div class="columns">
      <div class="column is-4 is-offset-4">
        <h3 class="title">Log in</h3>

        <form @submit.prevent="submitForm">
          <div class="field">
            <label>Username:</label>
            <div class="control">
              <input type="text" class="input" v-model="username">
            </div>
          </div>

          <div class="field">
            <label>Password:</label>
            <div class="control">
              <input type="password" class="input" v-model="password">
            </div>
          </div>
          <div class="notification is-dark 15px" v-if="errors.length">
            <p v-for="error in errors" v-bind:key="error">{{ error }}</p>
          </div>
          <div class="field">
            <div class="control">
              <button class="button is-dark">LogIn</button>
            </div>
          </div>

          <hr>

          or <router-link to="/SignUp">Click Here</router-link> To Sign UP!
        </form>
      </div>
    </div>
  </div>
</template>


<script>
import axios from  'axios'

export default ({
  name:'LogIn',
  data(){
    return {
      username: '',
      password: '',
      errors: []
    }
  },
  mounted(){

    document.title = 'Log-in | angelbook'
  },
  methods: {
    async submitForm(){
        axios.defaults.headers.common['Authorization'] = ""

        localStorage.removeItem("token")

        const formData = {
        username: this.username,
        password: this.password
       }

       await axios
        .post("/api/v1/token/login/", formData)
        .then(response => {
          const token = response.data.auth_token

          this.$store.commit('setToken', token)

          axios.defaults.headers.common['Authorization'] = 'Token' + token

          localStorage.setItem('token', token)

          const toPath = this.$route.query.to || '/cart'

          this.$router.push(toPath)
        })
        .catch(error => {
                    if (error.response) {
                        for (const property in error.response.data) {
                            this.errors.push('${property}: ${error.response.data(property)}')
                        }

                        console.log(JSON.stringify(error.response.data))
                    } else if (error.message) {
                        this.errors.push('Something went wrong. Please try again')
                        
                        console.log(JSON.stringify(error))
                    }
                })
    // })
        // .catch(error => {
        //   if (error.response){
        //     for (const property in error.response.data){
        //       this.errors.push('${property}: ${error.response.data(property)}')
        //     }
        //   } else {
        //      this.errors.push('something went wrong please try again ')

        //      console.log(JSON.stringify(error))

        //   }
        // })
    }
  }
})
</script>
