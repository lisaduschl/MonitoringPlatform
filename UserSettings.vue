<!--
This component changes the user settings(email address, password, data pulling frequency)
-->
<template>
    <div class="">
        <Modal v-show="showModal" @confirm="deleteAccount" @close="closeModal"></Modal>
        <Menu></Menu>
        <div id="right-panel" class="right-panel">
            <Header></Header>
            <div class="content">
                <div class="animated fadeIn">
                    <div class="col-md-6">
                            <div class="card-header">
                                <strong id="userSettingsHeader">User Settings</strong>
                            </div>
                            <div class="card-body card-block">
                                <form action="#" method="post" enctype="multipart/form-data" class="form-horizontal">
                                    <div class="row form-group">
                                        <div class="col col-md-4"><label for="email-input" class=" form-control-label">User Role:</label></div>
                                        <div class="col-12 col-md-8">{{this.user.role}}</div>
                                    </div>
                                    <div role="tablist">
                                      <b-card no-body class="mb-1" id="settingsEmailTab">
                                        <b-card-header header-tag="header" class="p-1" role="tab">
                                          <b-btn block href="#" v-b-toggle.accordion1 variant="info">Email Address</b-btn>
                                        </b-card-header>
                                        <b-collapse id="accordion1" accordion="my-accordion" role="tabpanel">
                                          <b-card-body class="elements">
                                            <div class="col col-md-4"><label for="email-input" class=" form-control-label">Change your Email Address:</label></div>
                                            <div class="col-12 col-md-8"><input type="email" id="email-input" v-model="user.email" placeholder="Enter Email" class="form-control"></div>
                                          </b-card-body>                            
                                          <button type="button" class="btn btn-primary" v-on:click="changeMail" id="saveEmailButton">Submit</button>
                                        </b-collapse>
                                      </b-card>
                                      <b-card no-body class="mb-1" id="settingsPasswordTab">
                                        <b-card-header header-tag="header" class="p-1" role="tab">
                                          <b-btn block href="#" v-b-toggle.accordion2 variant="info">Password</b-btn>
                                        </b-card-header>
                                        <b-collapse id="accordion2" accordion="my-accordion" role="tabpanel">
                                          <b-card-body class="elements">
                                            <div class="col col-md-4"><label for="password-input" class=" form-control-label">Change your Password:</label></div>
                                            <div class="col-12 col-md-8"><input type="password" id="password-input" v-model="user.password" placeholder="Enter Password" class="form-control"></div>
                                            <div class="col col-md-4"><label for="confirmPassword-input" class=" form-control-label">Confirm Password:</label></div>
                                            <div class="col-12 col-md-8"><input type="password" id="confirmPassword-input" v-model="user.confirmPassword" placeholder="Confirm Password" class="form-control"></div>
                                          </b-card-body>
                                          <button type="button" class="btn btn-primary" v-on:click="changePassword" id="savePasswordButton">Submit</button>
                                        </b-collapse>
                                      </b-card>
                                      <b-card no-body class="mb-1" id="settingsFrequencyTab">
                                        <b-card-header header-tag="header" class="p-1" role="tab">
                                          <b-btn block href="#" v-b-toggle.accordion3 variant="info">Update Frequency</b-btn>
                                        </b-card-header>
                                        <b-collapse id="accordion3" accordion="my-accordion" role="tabpanel">
                                          <b-card-body class="elements">
                                            <div class="col col-md-4"><label for="frequency-input" class=" form-control-label">Change the dashboard updating frequency (in seconds):</label></div>
                                            <div class="col-12 col-md-8"><input type="text" id="frequency-input" v-model="user.dashb_upd_freq" placeholder="Enter Frequency" class="form-control"></div>
                                          </b-card-body>
                                          <button type="button" class="btn btn-primary" v-on:click="updateFrequency" id="saveFrequencyButton">Submit</button>
                                        </b-collapse>
                                      </b-card>
                                    </div>
                                </form>
                            </div>
                            <div class="card-footer">
                              <button id="delete" type="button" v-show="this.$store.state.user.role !== 'admin'" @click="showModal = true" class="btn btn-primary pull">Delete Account</button>
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
  import backend_api from '../../gateways/backend_api';
  import {router} from '../router.js'
  import Header from './Header.vue'
  import Menu from './Menu.vue'
  import Modal from './Modal.vue'
  import axios from 'axios'

  export default {
    name: 'UserSettings',
    components: {
      Header,
      Menu,
      Modal
    },
    data () {
      return {
        user: {
          'email': '',
          'dashb_upd_freq': 0,
          'password': '',
          'confirmPassword': '',
          'role': ''
        },
        'showModal': false
      }
    },
    methods: {

      /**
       * Returns the user information.
       */
      fetchUser: function () {
        backend_api.get('users/'+this.$store.state.user.id).then(res => {
          if (res.data.meta.status === 'success') {
              this.user.email = res.data.data.email
              this.user._id = res.data.data._id
              this.user.dashb_upd_freq = res.data.data.dashb_upd_freq
              this.user.role= res.data.data.role

              console.log(this.user)
          }
        });
      },

      /**
       * Changes the email address of a user.
       */
      changeMail: function () {
        let email = this.user.email;
        function validateEmail(email) {
          var re = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
          return re.test(String(email).toLowerCase());
        }
        if (email === ""){
          toastr.error("Enter an Email Address!", "Warning", {"timeOut": "1500"})
        } else if (!validateEmail(email)) {
          toastr.error("The Email is incorrect!", "Warning", {"timeOut": "1500"})
        } else {
          var data = {
            email: this.user.email,
          }
          backend_api.patch('users/'+this.$store.state.user.id, data).then(res => {
            if (res.data.meta.status === 'success') {
              this.$toastr.success('Your email address was changed', 'Success')
            }
           },
           error => {
            console.log(error)
            this.$toastr.error(error, 'Warning', {"timeOut": "1500"})
           })
        }
      },

      /**
       * Changes the password of a user.
       */
      changePassword: function () {
        if (this.user.password === "") {
          this.$toastr.error('Please enter a Password', 'Warning')
        } else if (this.user.confirmPassword === "") {
          this.$toastr.error('Please confirm your Password', 'Warning')
        } else if (this.user.password.length < 5){
          this.$toastr.error('Your Password must contain at least 5 characters', 'Warning')
        } else if (this.user.password.length > 30){
          this.$toastr.error('Your Password must be shorter than 30 characters', 'Warning')
        } else if (this.user.password !== this.user.confirmPassword) {
          this.$toastr.error('Your Password is invalid', 'Warning')
        } else {
          let data = {password: this.user.password, password_repeat: this.user.confirmPassword};
          backend_api.patch('users/'+this.$store.state.user.id, data).then(res => {
            if (res.data.meta.status === 'success') {
              this.$toastr.success('Your password was changed', 'Success')
            }
          }, err => {
            this.$toastr.error(err, 'Warning')
          })
        }
      },

      /**
       * Updates the frequency with which the dashboard pulls new data from the backend.
       */
      updateFrequency: function () {
        this.$store.state.user.updateFrequency = this.user.dashb_upd_freq;
        var data = {
          dashb_upd_freq: this.user.dashb_upd_freq
        }
        backend_api.patch('users/'+this.$store.state.user.id, data).then(res => {
          if (res.data.meta.status === 'success') {
            this.$toastr.success('Dashboard updating frequency was changed', 'Success')
          }
        }, err => {
            this.$toastr.error(err, 'Warning')
          })
      },

      /**
       * Closes the confirmation modal box.
       */
      closeModal: function() {
        this.showModal = false;
      },

      /**
       * Deletes an account.
       */
      deleteAccount: function () {
        backend_api.delete('users/' + this.$store.state.user.id).then((response) => {
          if (response.data.meta.status === 'success') {
            this.$store.dispatch('logout')
              .then(() => {
                this.$router.push('/login', () => {
                  this.$toastr.success('Your account was deleted', {positionClass:'toast-bottom-full-width'});
                });
              })
          } else {
            alert(response.data.meta.message);
          }
        }, (error) => {
          console.log(error.response.data.meta.message);
        });
      }
    },

    /**
       * Displays the fetched data.
       */
    mounted: function () {
       this.fetchUser();
    }
  }

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.col-md-6 {
  max-width: 550px;
  margin-right: auto;
  margin-left: auto;
}
.card-header, .card-footer, .card-body, .card-block{
  background-color: white;
}
#delete {
  background-color:red;
  border-color: red;
  margin-left:0.5em;
}
.btn-info {
  background-color: #6c757d;
  border-color: #6c757d;
  color: #fff;
}
.btn-info:hover {
  background-color: #fff;
  color: #6c757d;
}
.btn-info:active {
  background-color: #fff !important;
  color: #6c757d !important;
  border-color: #6c757d !important;
}
.card-header.p-1 {
  padding: .0rem!important;
}
.btn.btn-primary {
  margin-left: 22px;
  margin-bottom: 20px;
}
.form-control-label {
  width: 300px;
}
.card-body {
  padding-left: 20px;
  padding-right: 20px;
}
.elements {
  padding-left: 5px;
}
#password-input {
  margin-bottom: 15px;
  width: 400px;
}
#email-input, #confirmPassword-input, #frequency-input {
  width: 400px;
}
</style>
