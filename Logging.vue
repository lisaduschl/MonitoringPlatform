<!--
This component displays the latest logs.
 -->
<template>
  <div class="">
    <!-- Left Panel -->
    <Menu></Menu>
    <!-- /#left-panel -->
    <!-- Right Panel -->
    <div id="right-panel" class="right-panel">
      <!-- Header-->
      <Header></Header>
      <!-- /#header -->
      <!-- Content -->
      <div class="content">
        <!-- Animated -->
        <div class="animated fadeIn">
          <!-- Orders -->
          <div class="orders">
            <div class="row">
              <div class="col-xl-12">
                <div class="card">
                  <div class="card-body">
                    <h4 class="box-title log-header" id="logsHeader">Logs</h4>
                    <v-data-table
                      :pagination.sync="pagination"
                      :headers="headers"
                      :items="desserts"
                      class="elevation-1"
                    >
                      <template slot="items" slot-scope="props">
                        <td>{{ props.item.type }}</td>
                        <td class="text-xs-left">{{ props.item.content }}</td>
                      </template>
                    </v-data-table>
                  </div>
                </div>
                <!-- /.card -->
              </div>
              <!-- /.col-lg-8 -->
            </div>
          </div>
          <!-- /.orders -->
        </div>
        <!-- .animated -->
      </div>
      <!-- /.content -->
      <div class="clearfix"></div>
      <!-- Footer -->
      <Footer></Footer>
      <!-- /.site-footer -->
    </div>
    <!-- /#right-panel -->
  </div>
</template>

<script>
import {} from '../assets/js/main.js'

import backend_api from '../../gateways/backend_api'
import Header from './Header.vue'
import Menu from './Menu.vue'
import Modal from './Modal.vue'
import axios from 'axios'

export default {
  name: 'Logging',
  data () {
    return {
        pagination: {
          rowsPerPage: 15
        },
        headers: [
          {
            text: 'Type',
            align: 'center',
            sortable: true,
            value: 'type'
          },
          {
            text: 'Content',
            align: 'center',
            sortable: true,
            value: 'content'
          }
        ],
        desserts: [

        ]
    }
  },
  components: {
    Header,
    Menu,
    Modal,
  },
  created () {
    function formatDate () {
      var cur_date = new Date()

      var year = cur_date.getFullYear()

      var month = '' + (cur_date.getMonth() + 1)

      var day = '' + cur_date.getDate()
      if (month.length < 2) month = '0' + month
      if (day.length < 2) day = '0' + day
      return [year, month, day].join('-')
    }
    backend_api.get('/logs?type=alert&date=' + formatDate()).then(res => {
      if (res.data.data !== ''){
        let alert_log = res.data.data.split('\n')
        alert_log.forEach((i, j) => {
          this.desserts.push({type: 'Alert', content: i})
        })
      }
    }, err => {
      console.err(err)
    })
    backend_api.get('/logs?type=api&date=' + formatDate()).then(res => {
      if (res.data.data !== '') {
        let api_log = res.data.data.split('\n');
        api_log.forEach((i,j) => {
          this.desserts.push({type: 'Api', content: i})
        })
      }
    }, err => {
      console.err(err)
    })
    backend_api.get('/logs?type=email&date=' + formatDate()).then(res => {
      if (res.data.data !== '') {
        let email_log = res.data.data.split('\n');
        email_log.forEach((i,j) => {
          this.desserts.push({type: 'Email', content: i})
        })
      }
    }, err => {
      console.err(err)
    })
    backend_api.get('/logs?type=node&date=' + formatDate()).then(res => {
      if (res.data.data !== ''){
        let node_log = res.data.data.split('\n');
        node_log.forEach((i, j) => {
          this.desserts.push({type: 'Node', content: i})
        })
      }
    }, err => {
      console.err(err)
    })
  }

}
</script>
