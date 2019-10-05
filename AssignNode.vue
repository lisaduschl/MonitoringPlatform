<!-- 
  This component is used to assign nodes to a basic user.
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
              <div class="col-lg-5">
                    <div class="card-header">
                        <p>Selected User: <strong>{{this.user.email}}</strong></p>
                    </div>
                    <div class="card-body card-block">
                        <div>
                            <div class="error-message"></div>
                            <table class="table" id="assignNodesTable">
                                <tr v-for="(node,idx) in nodes" :key="idx" >
                                    <td>
                                      <p-check class="p-default p-fill" @change="assignNodes($event, node._id)" :checked="checkedNodes[idx]" color="primary"></p-check>
                                    </td>
                                    <td>{{node.name}}</td>
                                </tr>
                            </table>
                        </div>
                    </div>
                    <div class="card-footer">
                        <router-link style="margin-left:0.5em;" :to="{path: '/userprofile/' + user._id}" class="btn btn-success pull float-right">Go to UserProfile</router-link>
                    </div>
                <b-pagination v-if="paginationNeeded" class="justify-content-center" size="sd" :total-rows="(10*pages)" :per-page="10" v-model="currentPage" @input="fetchNodes(currentPage)">
                </b-pagination>
              </div>
            </div>           
            <br>
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
    name: 'AssignNode',
    components: {
        Header,
        Menu,
        bPagination,
        Modal
    },
    data: function() {
      return {
        nodes: [],
        checkedNodes: [],
        user: {},
        error: "",
        currentPage: 1,
        pages : 1,
        showModal : false,
      }
    },
    computed: {
      paginationNeeded: function () {
        return this.pages > 1;
      },   
    },
    filters: {
      round: function (value) {
        if (value){
          return value.toFixed();
        } else {
          return;
        }
      }
    },
    methods: {

      /**
       * Returns the nodes of the selected user.
       */  
      fetchNodes: function (currentPage) {
        this.user_id = this.$route.params.user_id
        backend_api.get('nodes').then((response) => {
          this.nodes = response.data.data;
          this.pages = this.nodes.length / this.perpage;
          if (!this.nodes.length) {
            this.error = "There are no nodes registered!"
          } else {
            backend_api.get('users/'+this.user_id).then(res => {
            if (res.data.meta.status === 'success') {
                this.user = {
                    '_id': res.data.data._id,
                    'email': res.data.data.email,
                    'nodes': []
                }
                backend_api.get('users/'+this.user_id+'/nodes?page=' +currentPage).then(res => {
                    if (res.data.meta.status === 'success') {
                        this.user.nodes = res.data.data.map(item => item._id);
                        this.pages = parseInt(res.data.pages);
                        let checkedNodes = []
                        this.nodes.forEach((i, j) => {
                          let flag = 0;
                          this.user.nodes.forEach((k, l) => {
                            if (i._id === k) {
                              flag++;
                            }
                          })
                          console.log(flag);
                          if (flag === 0) {
                            checkedNodes[j] = 0
                          } else {
                            checkedNodes[j] = 1
                          }
                        })
                        this.checkedNodes = checkedNodes
                    }
                });

            }
        });
          }
        }, (error) => {
          console.log(error);
        });
      },

      /**
       * Assigns and unassigns nodes to the user.

       */  
      assignNodes(event, node) {
        let data = { node: node }
        if (event) {
          backend_api.post('users/' + this.user_id + '/nodes', data).then(res => {
              this.$toastr.success('Node is assigned successfully', 'Success', {'timeOut': '1500'})
          }, err => {
            this.$toastr.error('Node could not be assigned', 'Warning', {'timeOut': '1500'})
          })
        } else {
          backend_api.delete('users/' + this.user_id + '/nodes/' + node).then(res => {
            this.$toastr.success('Node is unassigned successfully', 'Success', {'timeOut': '1500'})
          }, err => {
            this.$toastr.error('Node could not be unassigned', 'Warning', {'timeOut': '1500'})
          })
        }
      }
    },
    mounted() {
      this.fetchNodes(this.currentPage);
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.col-lg-5{
  background-color: white;
  margin-right: auto;
  margin-left: auto;
  min-width: 600px;
  padding-bottom: 20px;
}
.card-header, .card-footer{
  background-color: white;
  border-bottom: 0;
  border-top: 0;
}
.card-body {
  padding: 10px;
}
p {
  color: black;
}
</style>
