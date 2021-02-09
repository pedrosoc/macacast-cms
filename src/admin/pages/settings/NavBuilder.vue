<template>
  <div class="nav-builder">
    <!-- navigation menu builder -->
    <h3 class="is-size-3">Criador de menu de navegação</h3>
    <div class="box">
      <div class="columns">

        <!-- link form -->
        <div class="column">
          <div class="field">
            <label class="label">ID do conteúdo</label>
            <div class="control">
              <input class="input" type="text" placeholder="ID (ex: 12345abcde)" v-model="id">
            </div>
          </div>
          <div class="field">
            <label class="label">Nome</label>
            <div class="control">
              <input class="input" type="text" placeholder="Nome (ex: Podcasts)" v-model="name">
            </div>
          </div>
          <div class="field">
            <label class="label">Caminho</label>
            <div class="control">
              <input class="input" type="text" placeholder="Caminho (ex: /podcasts)" v-model="route">
            </div>
          </div>
          <div class="field is-grouped">
            <div class="control">
              <button v-if="action === 'new'" class="button is-info" @click="addLink">Adicionar</button>
              <button v-if="action === 'edit'" class="button is-info" @click="updateLink">
                Atualizar
              </button>
              <button v-if="action === 'new-sub'" class="button is-info" @click="appendSubLink">
                Adicionar sub-link
              </button>
              <button v-if="action === 'edit-sub'" class="button is-info" @click="updateSubLink">
                Editar sub-link
              </button>
            </div>
            <div class="control">
              <button class="button" @click="clear">Cancelar</button>
            </div>
          </div>
        </div>

        <!-- menu visualization -->
        <div class="column">
          <p class="is-size-4">Menu</p>
          <ul v-for="(item, index) in menu" :key="index" class="nav-preview">
            <li>
              {{item.name}}: {{item.route}} (ID do conteúdo: {{item.id}})

              <span class="link-actions">
                <span class="has-text-danger fa fa-trash" @click="removeLink(item)"></span>
                <span v-if="index !== 0" class="has-text-success fa fa-arrow-up" @click="moveLinkUp(item, menu[index - 1])"></span>
                <span v-if="index !== menu.length - 1" class="fa fa-arrow-down" @click="moveLinkDown(item, menu[index + 1])"></span>
                <span class="has-text-info fa fa-edit" @click="editLink(item)"></span>
              </span>

              <!-- render children links -->
              <ul v-if="item.children" class="sub-nav-preview">
                <li v-for="(child, key) in item.children" :key="key">
                  {{child.name}}: {{child.route}}

                  <span class="sub-link-actions">
                    <span class="has-text-danger fa fa-trash" @click="removeSubLink(key, item)"></span>
                    <span class="has-text-success fa fa-arrow-up" @click="moveSubLinkUp(key, item)"></span>
                    <span class="fa fa-arrow-down" @click="moveSubLinkDown(key, item)"></span>
                    <span class="has-text-info fa fa-edit" @click="editSubLink(key, item)"></span>
                  </span>
                </li>
              </ul>
            </li>
          </ul>
        </div>

      </div>

    </div>
  </div>
</template>

<script>
import { settingsRef, navRef } from '@/admin/firebase_config'
export default {
  data () {
    return {
      key: '',
      link: '', // {'.key', name, route}
      id: '',
      name: '',
      route: '',
      action: 'new'
    }
  },
  computed: {
    isAbsolute () {
      return this.route.startsWith('http')
    }
  },
  firebase: {
    settings: {
      source: settingsRef,
      asObject: true
    },
    menu: {
      source: navRef
    }
  },
  methods: {
    addLink () {
      if (this.name && this.route) {
        this.$firebaseRefs.menu.push({
          id: this.id,
          name: this.name,
          route: this.route,
          isAbsolute: this.isAbsolute
        })
      }
      this.clear()
    },
    editLink (link) {
      this.id = link.id
      this.name = link.name
      this.route = link.route
      this.action = 'edit'
      this.key = link['.key']
      this.link = Object.assign({}, link)
    },
    updateLink () {
      delete this.link['.key']
      this.link.id = this.id
      this.link.name = this.name
      this.link.route = this.route
      this.link.isAbsolute = this.isAbsolute

      this.$firebaseRefs.menu.child(this.key).set(this.link)
      this.clear()
    },
    removeLink (item) {
      this.$firebaseRefs.menu.child(item['.key']).remove()
    },
    clear () {
      this.id = ''
      this.name = ''
      this.route = ''
      this.action = 'new'
    },
    moveLinkUp (item, previousItem) {
      let itemCopy = Object.assign({}, item)
      let previousItemCopy = Object.assign({}, previousItem)

      delete itemCopy['.key']
      delete previousItemCopy['.key']

      this.$firebaseRefs.menu.child(item['.key']).set(previousItemCopy)
      this.$firebaseRefs.menu.child(previousItem['.key']).set(itemCopy)
    },
    moveLinkDown (item, nextItem) {
      let itemCopy = Object.assign({}, item)
      let nextItemCopy = Object.assign({}, nextItem)

      delete itemCopy['.key']
      delete nextItemCopy['.key']

      this.$firebaseRefs.menu.child(item['.key']).set(nextItemCopy)
      this.$firebaseRefs.menu.child(nextItem['.key']).set(itemCopy)
    },
    addSubLink (link) {
      this.action = 'new-sub'
      this.key = link['.key']
    },
    appendSubLink () {
      this.$firebaseRefs.menu.child(this.key).child('children').push({
        id: this.id,
        name: this.name,
        route: this.route,
        isAbsolute: this.isAbsolute
      })
      this.clear()
    },
    moveSubLinkUp (currentKey, parent) {
      let childrenObject = {}
      let childrenArray = this.convertFirebaseObjToArray(parent.children)
      let previousKey = ''

      for (let i = 0; i < childrenArray.length; i++) {
        if (childrenArray[i]['.key'] === currentKey) {
          previousKey = childrenArray[i - 1]['.key']

          childrenObject[previousKey] = { ...childrenArray[i] }
          childrenObject[currentKey] = { ...childrenArray[i - 1] }

          delete childrenObject[previousKey]['.key']
          delete childrenObject[currentKey]['.key']
        } else {
          childrenObject[childrenArray[i]['.key']] = { ...childrenArray[i] }
          delete childrenObject[childrenArray[i]['.key']]['.key']
        }
      }

      this.$firebaseRefs.menu.child(parent['.key']).child('children').set(childrenObject)
    },
    moveSubLinkDown (currentKey, parent) {
      let childrenObject = {}
      let childrenArray = this.convertFirebaseObjToArray(parent.children)
      let nextKey = ''

      for (let i = childrenArray.length - 1; i >= 0; i--) {
        if (childrenArray[i]['.key'] === currentKey) {
          nextKey = childrenArray[i + 1]['.key']

          childrenObject[nextKey] = { ...childrenArray[i] }
          childrenObject[currentKey] = { ...childrenArray[i + 1] }

          delete childrenObject[nextKey]['.key']
          delete childrenObject[currentKey]['.key']
        } else {
          childrenObject[childrenArray[i]['.key']] = { ...childrenArray[i] }
          delete childrenObject[childrenArray[i]['.key']]['.key']
        }
      }

      this.$firebaseRefs.menu.child(parent['.key']).child('children').set(childrenObject)
    },
    convertFirebaseObjToArray (obj) {
      let array = []
      for (let key in obj) {
        array.push({ '.key': key, ...obj[key] })
      }
      return array
    },
    removeSubLink (key, parent) {
      this.$firebaseRefs.menu.child(parent['.key']).child('children').child(key).remove()
    },
    editSubLink (key, parent) {
      this.id = parent.children[key].id
      this.name = parent.children[key].name
      this.route = parent.children[key].route
      this.action = 'edit-sub'
      this.key = key
      this.link = Object.assign({}, parent)
    },
    updateSubLink () {
      this.$firebaseRefs.menu.child(this.link['.key']).child('children').child(this.key).update({
        id: this.id,
        name: this.name,
        route: this.route,
        isAbsolute: this.isAbsolute
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.nav-builder {
  .nav-preview {
    padding-left: 15px;
  }

  .sub-nav-preview {
    padding-left: 30px;
  }

  .link-actions,
  .sub-link-actions {
    display: none;
    span {
      cursor: pointer;
    }
  }

  .nav-preview > li:hover .link-actions {
    display: inline;
  }

  .sub-nav-preview > li:hover .sub-link-actions {
    display: inline;
  }

  .sub-nav-preview > li:first-child {
    .fa-arrow-up {
      display: none;
    }
  }

  .sub-nav-preview > li:last-child {
    .fa-arrow-down {
      display: none;
    }
  }
}
</style>
