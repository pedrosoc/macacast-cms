<template>
  <div class="navbar-container">
    <nav class="navbar container" id="navbar">

        <!-- modal for post delete -->
        <transition mode="out-in" name="fade">
        <modal class="modal" @close="showModal = false" :kind="kind" @confirmLogout='confirmLogout()' v-if="showModal" :header="header" />
        </transition>

        <!-- the navbar brand
        <div class="navbar-brand">
        <div class="navbar-burger burger" data-target="navbar" @click="toggleMenu">
            <span></span>
            <span></span>
            <span></span>
        </div>
        </div> -->

        <!-- the navbar right menu -->
        <div class="navbar-end-custom">
            <span class="navbar-item email">
                Logado como: {{currentUser.email}}
            </span>
            <span class="navbar-item sign-out" @click="signOut">
                <span class="has-text-centered">
                    Sair <i class="fa fa-sign-out fa-lg"></i>
                </span>
            </span>
            <!-- mobile navigation
            <a class="navbar-item is-hidden-desktop" @click="signOut" style="display: none">Logout</a>
            <div class="is-hidden-tablet">
                <router-link v-for="item in menu" :key="item.index" :to="item.path" class="menu-item">
                    <span class="navbar-item">{{ item.name }}</span>
                </router-link>
            </div>-->
        </div>

    </nav>
  </div>
</template>

<script>
import firebase from 'firebase/app'
import 'firebase/auth'
import modal from '@/admin/components/shared/Modal'
export default {
  name: 'navbar',
  data () {
    return {
      mobileMenuIsActive: false,
      currentUser: firebase.auth().currentUser,
      showModal: false,
      header: 'Você tem certeza que deseja sair?',
      kind: 'logout',
      baseUrl: process.env.BASE_URL,
      menu: [
        {
          name: 'Content',
          path: '/admin/content'
        },
        /*{
          name: 'Routing',
          path: '/admin/routing'
        },*/
        {
          name: 'Media',
          path: '/admin/media'
        },
        /*{
          name: 'Database',
          path: '/admin/database'
        },*/
        {
          name: 'Settings',
          path: '/admin/settings'
        }
      ]
    }
  },
  methods: {
    signOut () {
      this.showModal = true
    },
    confirmLogout () {
      // sign the current user out
      firebase.auth().signOut()
        .then(() => {
          this.showModal = false
          this.$router.push('/')
        })
        .catch(() => {
          console.log('sign out fails')
        })
    },
    toggleMenu () {
      this.mobileMenuIsActive = !this.mobileMenuIsActive
    }
  },
  components: {
    modal
  }
}

</script>

<style lang="scss">

$navbarBg: #fff;
$navbarColor: #444;

.navbar-container {
  margin-bottom: 10px;
  box-shadow: 0 8px 6px -6px #ccc;
  -moz-box-shadow: 0 8px 6px -6px #ccc;
  -webkit-box-shadow: 0 8px 6px -6px #ccc;

  #navbar {
    display: flex;
    align-items: center;
    justify-content: space-between;

    background-color: $navbarBg;
    min-height: 40px !important;
    height: 40px !important;
    z-index: 1;

    @media screen and (max-width: 768px) {
      height: 60px !important;
    }

    .logo {
        font-size: 28px;
        color: #2b2b2b;
        background: #fff;

        &:hover {
        background: whitesmoke;
        }
    }

    .navbar-item {
        color: $navbarColor;
        font-weight: bold;
        max-height: 100px;
        img {
        max-height: 3rem;
        margin-left: 1.2rem;
        }
    }

    span.email {
        margin-right: -10px;
        padding: 0;
    }

    .sign-out {
        cursor: pointer;
        padding: 0;
    }

    .navbar-end-custom {
      width: 100%;
      display: flex;
      align-items: center;
      justify-content: space-between;

      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;

      @media screen and (max-width: 768px) {
        flex-direction: column;
        align-items: flex-start;
        justify-content: center;
      }
    }

    .fa-sign-out {
        margin-left: 10px;
    }
  }

  .modal {
    z-index: 1025;
  }

  .nav-burger {
    position: fixed;
    right: 10px;
  }

  @media screen and (max-width: 1023px) {
    #navbar {
      .navbar-menu {
        width: auto;
        height: auto;
        right: 0;
      }
    }
  }
}
</style>
