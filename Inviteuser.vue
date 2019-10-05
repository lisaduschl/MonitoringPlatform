<!--
  This component is used to invite a new user.
 -->
<template>
  <div class="">
    <Menu></Menu>
    <div id="right-panel" class="right-panel">
      <Header></Header>
      <div class="content">
        <div class="animated fadeIn">
          <div class="row">
            <div class="col-md-6">
              <div class="card-header">
                <strong id="inviteUserHeader">Invite User</strong>
              </div>
              <div class="card-body card-block">
                <div class="row form-group">
                  <div class="col-12 col-md-12">
                    <input
                      type="email"
                      id="email"
                      name="email-input"
                      placeholder="Email Address"
                      class="form-control"
                      v-model="email"
                    >
                  </div>
                </div>
                <div class="row form-group">
                  <div class="col-12 col-md-12">
                    <select class="form-control" v-model="role" id="roleSelect">
                      <option selected>Basic User</option>
                      <option>Extended User</option>
                    </select>
                  </div>
                </div>
              </div>
              <div class="card-footer">
                <button v-on:click="invite" class="btn btn-primary" id="inviteButton">
                   Invite
                </button>
              </div>
            </div>
            <div class="col-md-6">
              <div class="card-header">
                <strong id="tokenHeader">Authentication Token Expiration</strong>
              </div>

              <div class="card-body card-block">
                <div class="row form-group">
                  <div class="col-12 col-md-12">
                    <input
                      type="text"
                      class="form-control"
                      v-model="token_exp"
                      id="tokenExpiration"
                    >
                  </div>
                </div>
              </div>
              <div class="card-footer">
                <button v-on:click="tokensave" class="btn btn-primary" id="saveTokenButton">
                  Save
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="clearfix"></div>
      <Footer></Footer>
    </div>
  </div>
</template>

<script>
import {} from '../assets/js/main.js'
import Header from './Header.vue'
import Menu from './Menu.vue'
import axios from 'axios'
export default {
  name: 'Inviteuser',
  components: {
    Header,
    Menu
  },
  data () {
    return {
      email: '',
      token_exp: '',
      role: 'Basic User'
    }
  },
  created() {
    this.$store.dispatch('get_config').then(res => {
      this.token_exp = res.data.data.token_exp
    }, err => {
      toastr.error(err, "Warning")
    })
  },
  methods: {

    /**
       * Checks for a valid email address and sends email address and user role to the backend.

       */
    invite: function () {
      function validateEmail(email) {
          var re = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
          return re.test(String(email).toLowerCase());
      }
      let token = this.$store.state.user.authToken
      let email = this.email
      if (email === ""){
        toastr.error("Enter an Email Address!", "Warning", {"timeOut": "1500"})
      } else if (!validateEmail(email)) {
        toastr.error("The Email is incorrect!", "Warning", {"timeOut": "1500"})
      } else {
        let role = ''
        if (this.role === 'Basic User') { role = 'basic'} else { role = 'extended' }
        let data = {email: this.email, role: role}
        this.$store.dispatch('invite_user', data).then(response => 
          {
            console.log(response.data)
            this.$toastr.success('User was successfully invited!', 'Success', {"timeOut": "1500"})
          },
          error => {
            console.log(error)
            this.$toastr.error('Fail: Duplicated Email! Please retry.', 'Warning', {"timeOut": "1500"})
          })
      }
    },

    /**
       * Saves the new token value.

       */
    tokensave: function () {
      this.$store.dispatch('set_config', {token_exp: this.token_exp}).then(res => {
        this.$toastr.success('Successfully set!', 'Success', {'timeOut': '1500'})
      }, err => {
        this.$toastr.error('Failed to set new Server Configuration', 'Warning', {'timeOut': '1500'});
      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.card-body, .card-block{
  background-color: white;
}
.card-header, .card-footer{
  background-color: white;
}
</style>
<style src="../assets/css/cs-skin-elastic.css"></style>
<style src="../assets/css/style.css"></style>
