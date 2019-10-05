<!--
This component shows the menu bar on the left side of the dashboard.
Author: Lisa
 -->
<template>
    <aside id="left-panel" class="left-panel">
        <nav class="navbar navbar-expand-sm navbar-default">
            <div id="main-menu" class="main-menu">
                <ul class="nav navbar-nav">
                    <li>
                      <router-link :to="{ path: '/', params: {  option: 'index' }}" id="nodesPage">
                          <i class="menu-icon fa fa-laptop"></i>Nodes
                      </router-link>
                    </li>
                    <li v-show="this.$store.state.user.role !== 'basic'">
                      <router-link :to="{ path: '/userlist', params: {  option: 'userlist' }}" id="usersPage">
                          <i class="menu-icon fa fa-th"></i>Users
                      </router-link>
                    </li>
                    <li>
                     <router-link :to="{ path: '/alerts' }" id="alertsPage">
                        <i class="menu-icon fa fa-tasks"></i>Alerts
                    </router-link>
                    </li>
                    <li>
                     <router-link :to="{ path: '/notifications' }" id="notificationsPage">
                        <i class="menu-icon fa fa-bell-o"></i>Notifications
                    </router-link>
                    </li>
                    <li v-show="this.$store.state.user.role === 'admin'">
                     <router-link :to="{ path: '/inviteuser' }" id="serverSettingsPage">
                        <i class="menu-icon fa fa-cogs"></i>Settings
                    </router-link>
                    </li>
                    <li @click="colorize($event)" v-show="this.$store.state.user.role === 'admin'">
                     <router-link :to="{ path: '/logging' }" id="logsPage">
                        <i class="menu-icon fa fa-list"></i>Logging
                    </router-link>
                    </li>
                </ul>
            </div>
        </nav>
    </aside>
</template>

<script>
    const clear = () => {
    };
export default {
  name: 'Menu',
  data () {
    return {
      msg: 'Menu page'
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
