<template>
  <div class="sidebar-container">
    <div class="menu sidebar container" id="sidebar">
        <router-link class="sidebar-logo" to="/">
            MACACAST CMS
        </router-link>

        <div :class="['dropdown', 'is-hidden-tablet', showMobileMenu ? 'is-active' : '']" @click="toggleMobileMenu">
            <div class="dropdown-trigger">
                <button class="button" aria-haspopup="true" aria-controls="dropdown-menu">
                <span>Menu</span>
                <span class="icon is-small">
                    <i class="fa fa-angle-down" aria-hidden="true"></i>
                </span>
                </button>
            </div>
            <div class="dropdown-menu" id="dropdown-menu" role="menu">
                <div class="dropdown-content">
                    <div v-for="item in menu" :key="item.index">
                        <router-link tag="a" :to="item.path" class="dropdown-item">
                            {{item.name}}
                        </router-link>

                        <router-link v-if="item.dropdown && contents" v-for="content in contents" :key="content['.key']" tag="a" :to="'/admin/content/' + content['.key']" class="dropdown-item">
                            <span class="sub-dropdown-item">{{content.name}}</span>
                        </router-link>
                    </div>
                </div>
            </div>
        </div>

        <ul class="sidebar-menu-list is-hidden-mobile">
            <router-link v-for="item in menu" :key="item.index" tag="li" :to="item.path" class="menu-item">
                <div class="menu-item-link">
                    <div>{{ item.name }}</div>
                    <i :class="['fa', item.icon, 'icon']"></i>
                </div>

                <!-- drop down menu -->
                <ul class="menu-item-dropdown" v-if="item.dropdown && contents">
                    <router-link v-for="content in contents" :key="content['.key']" tag="li" :to="'/admin/content/' + content['.key']">
                        <div>
                        {{ content.name }}
                        </div>
                    </router-link>
                    <router-link :to="item.dropdown[0].path" tag="li">
                        {{item.dropdown.path}}
                        <div>
                        {{ item.dropdown[0].name }}
                        </div>
                    </router-link>
                </ul>
            </router-link>
        </ul>
    </div>
  </div>
</template>

<script>
import { contentsRef } from '@/admin/firebase_config'
export default {
  name: 'sidebar',
  firebase: {
    contents: contentsRef
  },
  data () {
    return {
      // this array contains the all the links in the sidebar
      loaded: true,
      showMobileMenu: false, 
      menu: [
        {
          name: 'Conteúdo',
          path: '/admin/content',
          icon: 'fa-wrench',
          dropdown: [
            {
              name: 'Adicionar/editar categorias',
              path: '/admin/content',
              icon: 'fa-file-text',
              supports: null
            }
          ]
        },
        /*{
          name: 'Rotas',
          path: '/admin/routing',
          icon: 'fa-random'
        },*/
        {
          name: 'Imagens',
          path: '/admin/media',
          icon: 'fa-picture-o'
        },
        /*{
          name: 'Database',
          path: '/admin/database',
          icon: 'fa-database'
        },*/
        {
          name: 'Configurações',
          path: '/admin/settings',
          icon: 'fa-gear'
        }
      ],
      helpbtn: {
        name: 'Ajuda',
        path: 'https://github.com/tamiat/tamiat/blob/master/README.md',
        icon: 'fa-question'
      }
    }
  },
  methods: {
    toggleMobileMenu () {
      this.showMobileMenu = !this.showMobileMenu
    }
  }
}

</script>

<style lang="scss">
$sidebarBg: #2b2b2b;
$sidebarColor: #ffffff;
$sidebarWidth: 115px;
$sidebarHeight: 80px;

.sidebar-container {
  background: $sidebarBg;

  #sidebar {
    height: $sidebarHeight;
    min-width: 45px;
    background: $sidebarBg;
    box-shadow: 0 2px 3px rgba(17, 17, 17, 0.1), 0 0 0 1px rgba(17, 17, 17, 0.1);

    display: flex;
    justify-content: space-between;
    align-items: center;

    @media screen and (max-width: 768px) {
        height: 120px;
        flex-direction: column;
        align-items: flex-start;
        justify-content: center;
    }

    .dropdown {
        .button {
            padding: 0;
        }

        .button, .dropdown-content, .dropdown-item {
            color: $sidebarColor;
            background-color: $sidebarBg;
            border-color: $sidebarBg;
        }

        .sub-dropdown-item {
            padding-left: 1.5rem;
            border-left: 1px solid #fff;
        }
    }

    .sidebar-logo {
        font-size: 30px;
        color: #fff;
        font-weight: bold;
    }
    
    .sidebar-menu-list {
        display: flex;
    }

    .menu-item-link {
        display: flex;
        align-items: center;
    }

    li {
        display: block;
        text-align: center;
        color: $sidebarColor;
        padding: 0px 25px;
        position: relative;
        cursor: pointer;
        
        // border-right: 1px solid #666666;

        &:last-child {
            padding-right: 0;
        }

        &:hover {
        & .menu-item-dropdown {
            display: block;
        }
        }
    }

    .menu-item.router-link-active {
        //background: $sidebarColor;
        //color: $sidebarBg;
    }

    .menu-item-dropdown {
        display: none;
        position: absolute;
        background: $sidebarBg;
        z-index: 1023;
        
        padding-top: 28px;
        right: 0;

        & li {
        padding: 15px;
        }

        & li.router-link-exact-active {
        // background: $sidebarColor;
        // color: $sidebarBg;
        }

    }

    .icon {
        font-size: 18px;
        width: fit-content;
        height: fit-content;
        margin-left: 10px;
    }
  }
}
</style>
