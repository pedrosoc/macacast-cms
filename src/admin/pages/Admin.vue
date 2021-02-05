<template>
  <div class="admin bulma" id="admin">
    <!-- The admin page layout -->

    <!-- The admin header-->
    <header class="has-sub">
      <div class="head-container">
          <sidebar></sidebar>
          <navbar></navbar>
      </div>
    </header>
    <!--The admin main page for a sidebar and content-->
    <div class="container">
      <!-- The admin page content -->
      <div class="admin-wrapper">
        <section class="hero is-medium" v-if="$route.name === 'Admin'">
          <div class="hero-body ">
            <div class="container has-text-centered">
              <router-link to="/">
                <img src="/static/img/macacast-logo-160.png" alt="Tamiat CMS logo">
              </router-link>
              <div class="titleAdmin">
                <h1 class="title">
                 Bem-vindo ao Macacast CMS
                </h1>
              </div>
              <p>Version v0.1</p>
            </div>
          </div>
        </section>
        <router-view :key="$route.path" />
      </div>

    </div>
  </div>
</template>

<script>
import Navbar from '@/admin/components/layout/Navbar'
import Sidebar from '@/admin/components/layout/Sidebar'
import firebase from 'firebase/app'
import 'firebase/auth'

export default {
  name: 'admin',
  components: {
    'navbar': Navbar,
    'sidebar': Sidebar
  },
  // prevent unauthenticated users from accessing the admin area
  // and direct them to the login page
  beforeRouteEnter (to, from, next) {
    // get the current logged in user
    firebase.auth().onAuthStateChanged((user) => {
      if (user) {
        // continue to /admin
        next()
      } else {
        // redirect to /login
        next('/login')
      }
    })
  }
}

</script>

<style lang="sass">
@import '../styles/index'
</style>
