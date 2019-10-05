<!--
  This component shows the user list of registered users.
 -->
<template>
  <div class="">
    <Menu></Menu>
    <div id="right-panel" class="right-panel">
      <Header></Header>
      <div class="content">
        <div class="animated fadeIn">
          <div class="orders">
            <div class="row">
              <div class="col-xl-12">
                <div class="card">
                  <div class="card-body">
                    <h4 class="box-title" id="header">Users</h4>
                  </div>
                  <div class="card-body--">
                    <div v-if="users.length" class="table-stats order-table ov-h">
                      <table class="table">
                        <thead>
                          <tr>
                            <th>EMAIL ADDRESS</th>
                            <th> USER ROLE</th>
                            <th></th>
                          </tr>
                        </thead>
                        <tbody id="usersList">
                          <tr v-for="user in users" v-bind:key="user._id">
                            <td>
                              <span class="email">{{user.email}}</span>
                            </td>
                            <td>
                              <span class="role">{{user.role}}</span>
                            </td>
                            <td>
                              <router-link
                                :to="{ path: '/userprofile/'+user._id, params: {  option: 'userprofile' }}"
                              >
                                <button type="button" class="btn btn-primary">View Profile</button>
                              </router-link>
                            </td>
                          </tr>
                        </tbody>
                      </table>
                    </div>
                  </div>
                </div>
                <b-pagination v-if="paginationNeeded" class="justify-content-center" size="sd" :total-rows="(10*pages)" :per-page="10" v-model="currentPage" @input="fetchUsers(currentPage)">
                </b-pagination>
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
import {} from '../assets/js/main.js';
import backend_api from '../../gateways/backend_api';
import Header from './Header.vue';
import Menu from './Menu.vue';
import bPagination from 'bootstrap-vue/es/components/pagination/pagination';
import axios from 'axios';

export default {
  name: 'UserList',
  components: {
    Header,
    Menu,
    bPagination
  },
  data () {
    return {
      users: [],
      currentPage: 1,
      pages: 1
    }
  },
   methods: {

      /**
       * Returns the registered users.
       */ 
      fetchUsers: function (currentPage) {
        let path = 'users?page=' + currentPage;
        backend_api.get(path).then(res => {
            if (res.data.meta.status === 'success') {
                this.users = res.data.data;
                this.pages = parseInt(res.data.pages);
            }
            if (!this.users.length) {
              this.error = 'There are no registered users'
            }
        });
    }
  },
  computed: {
      paginationNeeded: function () {
        return this.pages > 1;
      }
    },
  mounted() {
    this.fetchUsers(this.currentPage);
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.email {
  text-transform: lowercase !important;
}
</style>
