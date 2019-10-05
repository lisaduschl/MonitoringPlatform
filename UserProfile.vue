<!--
This component shows the user profile of a user.
Authors: Lisa & Dariia(part with modal window and delete account)
-->
<template>
  <div class="">
    <Modal v-show="showModal" @confirm="deleteAccount" @close="closeModal"></Modal>
    <Menu></Menu>
    <div id="right-panel" class="right-panel">
      <Header></Header>
      <div class="content">
        <div class="animated fadeIn">
           <div class= "col-md-6">
             <div class="card-header" id="userProfileHeader">
              Profile of <strong id="email">{{user.email}}</strong>
            </div>
            <div class="card-body card-block">
              <form action="#" method="post" enctype="multipart/form-data" class="form-horizontal">
                <div class="row form-group">
                  <div class="col col-md-3">
                    <label class="form-control-label">Email Address:</label>
                  </div>
                  <div class="col-12 col-md-9">
                    <p class="form-control-static">{{user.email}}</p>
                  </div>
                </div>
                <div class="row form-group"  v-if="this.$store.state.user.role === 'admin' && this.old_role !== 'admin'">
                  <div class="col col-md-3">
                    <label for="select" class="form-control-label">User Role:</label>
                  </div>
                  <div class="col-12 col-md-9">
                    <select v-model="user.role" class="form-control">
                      <option value="basic" >Basic User</option>
                      <option value="extended" >Extended User</option>
                      <option value="admin" >Admin</option>
                    </select>
                  </div>
                </div>
                <div class="row form-group" v-else>
                  <div class="col col-md-3">
                    <label class="form-control-label">Role</label>
                  </div>
                  <div class="col-12 col-md-9">
                    <p class="form-control-static">{{user.role}}</p>
                  </div>
                </div>
                <div class="row form-group" v-if="this.old_role === 'basic'">
                  <div class="col col-md-3">
                    <label class="form-control-label">Monitored Nodes:</label>
                  </div>
                  <div class="col-12 col-md-9">
                     <p v-for="node in user.nodes" v-bind:key="node._id">
                       <router-link :to="{ path: '/nodes/'+node._id}"> {{node.name}} </router-link>
                     </p>
                  </div>
                </div>
              </form>
            </div>
            <div class="card-footer">
              <button type="button"  v-if="this.old_role !== 'admin'" class="btn btn-primary" v-on:click="submitChanges" id="saveButton">
                Submit
              </button>
              <button type="button" v-show="showDeleteButton" style="margin-left:0.5em;" @click="showModal = true" class="btn btn-danger pull" id="deleteButton">Delete</button>
              <button type="button" v-show="showAssignButton" style="margin-left:0.5em;" @click="assignNodes" class="btn btn-success pull" id="assignNodesButton">Assign Nodes</button>
            </div>
           </div>
            <b-pagination v-if="paginationNeeded" class="justify-content-center" size="sd" :total-rows="(10*pages)" :per-page="10" v-model="currentPage" @input="fetch(currentPage)">
        </b-pagination>
        </div>
      </div>
      <div class="clearfix"></div>
      <Footer></Footer>
    </div>
  </div>
</template>

<script>
import {} from '../assets/js/main.js';
import backend_api from '../../gateways/backend_api';
import Header from './Header.vue';
import Menu from './Menu.vue';
import Modal from './Modal.vue';
import bPagination from 'bootstrap-vue/es/components/pagination/pagination';

export default {
  name: 'UserProfile',
  components: {
    Header,
    Menu,
    Modal,
    bPagination
  },
  data () {
    return {
      user: [],
      old_role: '',
      user_id: '',
      showModal: false,
      currentPage: 1,
      pages: 1
    }
  },
  computed: {
    showDeleteButton: function() {
      return this.$store.state.user.role === 'admin' && this.old_role !== 'admin';
    },
    showAssignButton: function() {
      return this.$store.state.user.role !== 'basic' && this.old_role === 'basic';
    },
    paginationNeeded: function () {
        return this.pages > 1;
      }
  },
  created: function () {
    this.fetch()
  },
  methods: {
    
    /**
     * Returns user information to be displayed on the profile.
     * 
     * @author Lisa
     */
    fetch: function (currentPage) {
      this.user_id = this.$route.params.user_id;
      backend_api.get('users/'+this.user_id).then(res => {
          if (res.data.meta.status === 'success') {
              this.user = {
                '_id': res.data.data._id,
                'email': res.data.data.email,
                'role': res.data.data.role,
                'nodes': []
              }
              this.old_role = this.user.role;

              //  Get nodes of selected user.
              backend_api.get('users/'+this.user_id+'/nodes?page='+currentPage).then(res => {
                if (res.data.meta.status === 'success') {
                  console.log(res.data);
                  this.user.nodes = res.data.data;
                  this.pages = parseInt(res.data.pages);
                }
              });
          }
      });
    },

    /**
       * Saves the changes (change user's role) when clicking the "submit"-button.
       * 
       * @author Lisa
       */
    submitChanges: function (event) {
     var data = {
       email: this.user.email,
       role: this.user.role
      }
      console.log(data)
      backend_api.patch('users/'+this.user_id, data).then(res => {
          if (res.data.meta.status === 'success') {
            this.$toastr.success("Successfully updated.");
            this.fetch();
          }
      })
     
    },

    /**
     * Directs to the assign nodes page.
     * 
     * @author Lisa
     */
    assignNodes: function() {
      this.$router.push('/assignnode/'+ this.user._id);
    },
    closeModal: function() {
      this.showModal = false;
    },
    deleteAccount: function () {
      backend_api.delete('users/' + this.user._id).then((response) => {
        if (response.data.meta.status === 'success') {
          this.$router.push('/userlist', () => {
            this.$toastr.success('Account was successfully deleted');
          });
        } else {
          alert(response.data.meta.message);
        }
      }, (error) => {
        console.log(error.response.data.meta.message);
      });
    },
  
  },
  mounted() {
    this.fetch(this.currentPage);
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.col-md-6{
  background-color: white;
  margin-right: auto;
  margin-left: auto;
  min-width: 500px;
}
.card-header, .card-footer{
  background-color: white;
}
</style>
