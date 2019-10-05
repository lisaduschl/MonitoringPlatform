<!--
  This component shows the dashboard notifications.
  Author: Lisa
 -->
<template>
  <div class="">
    <Modal v-show="showModal" @confirm="deleteNotification" @close="closeModal"></Modal>
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
                    <div class="row">
                      <!-- <div class="col-xl-1">
                        <h4 class="box-title">Notifications</h4>
                      </div>  -->
                      <div class="col-xl-3" id="searching">
                        <input type="text" v-model="search" class="form-control" placeholder="Search for a Notification"/>
                      </div>
                    </div>
                  </div>
                  <div class="card-body--">
                    <div class="table-stats order-table ov-h">
                      <table class="table">
                        <thead>
                          <tr>
                            <!-- <th class="serial">#</th> -->
                            <th>Alert</th>
                            <th>Node</th>
                            <th>Message</th>
                            <th>Date/Time</th>
                            <th></th>
                          </tr>
                        </thead>
                        <tbody>
                          <tr v-for="(notification) in paginatedData" v-bind:key="notification._id">
                            <!-- <td class="serial">{{index+1}}</td> -->
                            <td>
                              <span >{{notification.alert.name}}</span>
                            </td>
                            <td>
                              <span >{{notification.node.name}}</span>
                            </td>
                             <td>
                              <span >{{notification.message}}</span>
                            </td>
                             <td>
                              <span >{{new Date(notification.timestamp).getDate() + '.' + (new Date(notification.timestamp).getMonth() + 1) + '.' + new Date(notification.timestamp).getFullYear() + ' ' + new Date(notification.timestamp).getHours() + ':' + new Date(notification.timestamp).getMinutes() + ':' + new Date(notification.timestamp).getSeconds()}}</span>
                             </td>
                             <td>
                              <button id= "delete" type="button" class="btn btn-primary" v-on:click="focus_id=notification._id; showModal=true">
                                Delete
                              </button>
                             </td>
                          </tr>
                        </tbody>
                      </table>
                    </div>
                  </div>
                </div>
                <b-pagination v-if="paginationNeeded" class="justify-content-center" size="sd" :total-rows="notifications.length" v-model="currentPage" :per-page="perpage">
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
import bPagination from 'bootstrap-vue/es/components/pagination/pagination'
import Modal from './Modal.vue'

export default {
  name: 'Notifications',
  components: {
    Header,
    Menu,
    bPagination,
    Modal
  },
  data () {
    return {
      search: '',
      focus_id: '',
      showModal: false,
      currentPage: 1,
      perpage: 20,
      filterlist: [],
      // notifications: []
    }
  },
   methods: {
      fetch: function () {
            backend_api.get('users/'+this.$store.state.user.id+'/notifications').then(res => {
              if (res.data.meta.status === 'success') {
                   this.notifications = res.data.data;
                   console.log(this.notifications);
                   this.filterlist = this.notifications;
              }
          }, (error) => {
            console.log(error);
          });
      },
      closeModal: function () {
        this.showModal = false
      },
      deleteNotification: function () {
        backend_api.delete('users/' + this.$store.state.user.id+'/notifications/'+this.focus_id).then((response) => {
          if (response.data.meta.status === 'success') {
            this.$toastr.success('Notification has been deleted successfully.')
            this.fetch()
            this.showModal = false
          } else {
            alert(response.data.meta.message)
            this.showModal = false
          }
        }, (error) => {
          console.log(error.response.data.meta.message)
          this.showModal = false
        })
      },
    },
    computed: {
      paginationNeeded: function () {
        return this.filterlist.length > this.perpage;
      },
      paginatedData: function () {
        //alert(this.currentPage);
        const start = (this.currentPage-1) * this.perpage,
        end = start + this.perpage;
        return this.filterlist.slice(start, end);
      }
    },
    watch:{
      search (){
          this.filterlist = this.notifications.filter(item => {
            return item.alert.name.toLowerCase().includes(this.search.toLowerCase()) ||
             item.node.name.toLowerCase().includes(this.search.toLowerCase());
        })
      }
    },
  mounted() {
    this.fetch()
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#searching{
  float: right;
}
.box-title{
  position: fixed;
}
button, button:hover {
  background-color: red;
  border-color: red;
}
</style>