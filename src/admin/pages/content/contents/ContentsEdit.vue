<template>
  <div class="box">
    <loading color="#3b9169" :active.sync="isLoading" :can-cancel="false" :is-full-page="fullPage"></loading>


    <div v-if="content.state === 'saved'">
        <b>Teste sua publicação no site através desse link:</b>
        <br />
        <a :href="'https://macacast.com.br' + this.categoryRoute + '/' + this.content['.key'] + '/preview'" target="_blank">{{ 'https://macacast.com.br' + this.categoryRoute + '/' + this.content['.key'] + '/preview' }}</a> 
        <br /><br />
    </div>
    <div v-if="content.state !== 'saved'">
        <b>Veja essa publicação no site através desse link:</b>
        <br />
        <a :href="'https://macacast.com.br' + this.categoryRoute + '/' + this.content['.key']" target="_blank">{{ 'https://macacast.com.br' + this.categoryRoute + '/' + this.content['.key'] }}</a> 
        <br /><br />
    </div>
    <div class="columns">

      <div class="column is-two-thirds">

        <!-- Textbox fields -->
        <div class="field" v-for="(field, index) in fields" :key="index" v-if="field.type === 'textbox'">
          <label class="label">{{ field.name }}</label>
          <div class="control">
            <input type="text" class="input" maxlength="255" required :placeholder="field.name" v-model="content[field.bdName]">
          </div>
        </div>

        <!-- Textarea fields -->
        <div class="field" v-for="(field, index) in fields" :key="index" v-if="field.type === 'textarea'">
          <label class="label">{{ field.name }}</label>
          <div class="control">
            <textarea class="input" v-model="content[field.bdName]" :placeholder="field.name" v-text="content[field.bdName]"></textarea>
          </div>
        </div>

        <!-- Rich text fields - vue-quill-editor plugin -->
        <div v-for="(field, index) in fields" :key="index" v-if="field.type === 'richtextbox'">
          <quill-editor v-model="content[field.bdName]" :options="editorOptions">
          </quill-editor>
          <input type="file" id="getImage" style="display: none;" @change="uploadImage">
        </div>

        <!-- Category (select) fields -->
        <br>
        <div class="field" v-for="(field, index) in fields" :key="index" v-if="field.type === 'select'">
          <label class="label">{{ field.name }}</label>
          <div class="columns">
            <div class="column">
              <div class="select margin-select">
                <select v-model="content[field.bdName].selected">
                  <option v-for="(selectOption, selectIndex) in content[field.bdName].options" :key="selectIndex">{{ selectOption }}</option>
                </select>
              </div>
            </div>
            <div class="column">
              <!--area to delete options-->
              <div v-for="(option, optionKey) in content[field.bdName].options" :key="optionKey" >
                <span @click="removeTag(optionKey, field.bdName, true)" class="tag is-info pointer">{{option}}
                <button class="delete is-small"></button>
              </span>
              </div>
            </div>
          </div>
          <!--area to enter options-->
          <div class="control">
            <input type="text" class="input" :placeholder="field.name" @blur="styleOptions(field.bdName)" @keyup.enter="styleOptions(field.bdName)" v-model="selectOptionsRow" maxlength="255">
          </div>
          <p>Separa as opções por vírgula (Ex: "Suco,Maça,Pêra")</p>
        </div>

        <!-- Integer fields -->
        <br>
        <div class="field" v-for="(field, index) in fields" :key="index" v-if="field.type === 'integer'">
          <label class="label">{{ field.name }}</label>
          <div class="columns">
            <div class="column is-one-third">
              <input type="number" :placeholder="field.name" class="input" v-model="content[field.bdName]">
            </div>
          </div>
        </div>

        <!-- Boolean fields -->
        <br>
        <div class="field" v-for="(field, index) in fields" :key="index" v-if="field.type === 'boolean'">
          <label class="label">{{ field.name }}</label>
          <div class="columns">
            <div class="column is-one-third">
              <span>Name</span>
              <input :placeholder="field.name + ' name'" class="input" v-model="content.bool">
            </div>
          </div>
          <div class="columns">
            <div class="column">
              <label class="checkbox">
                <input type="checkbox">
                {{ content.bool }}
              </label>
            </div>
          </div>
        </div>

        <!-- Url fields -->
        <br>
        <div class="field" v-for="(field, index) in fields" :key="index" v-if="field.type === 'url'">
          <label class="label">{{ field.name }}</label>
          <div class="columns">
            <div class="column is-two-fifths">
              <span>Nome</span>
              <input :placeholder="field.name + ' name'" class="input" v-model="content[field.bdName].name">
            </div>
            <div class="column is-two-fifths">
              <span>Link</span>
              <input :placeholder="field.name + ' link'" class="input" v-model="content[field.bdName].link">
            </div>
          </div>
          <div class="columns">
            <div class="column">
              <span>Visualização: </span>
              <a :href="content[field.bdName].link">{{ content[field.bdName].name }}</a>
            </div>
          </div>
        </div>
        <!---->
      </div>

      <!-- Right sidebar -->
      <div class="column is-one-third">

        <!-- Tag fields -->
        <div class="field" v-for="(field, index) in fields" :key="index" v-if="field.type === 'tags'">
          <label class="label">{{ field.name }}</label>
          <div class="control">
            <div class="tags tagscontainer">
              <span @click="removeTag(tagKey, field.bdName)" v-for="(tag, tagKey) in content[field.bdName]" :key="tagKey" class="tag is-info pointer">{{tag}}
                <button class="delete is-small"></button>
              </span>
              <input :placeholder="field.name" @blur="styleTags(field.bdName)" @keyup.enter="styleTags(field.bdName)" type="text" class="input" maxlength="255" v-model="inputData">
            </div>
            <p>Separa as opções por vírgula (Ex: "Suco,Maça,Pêra")</p>
          </div>
        </div>

        <!-- Image fields -->
        <div class="field" v-for="(field, index) in fields" :key="index" v-if="field.type === 'image'">
          <label class="label">{{ field.name }}</label>
          <div class="control">
            <img v-if="imagePreview || content[field.bdName]" :src="imagePreview || content[field.bdName]">
            <div class="file">
              <label class="file-label">
                <input @change="uploadFeaturedImage($event, field.bdName)" class="file-input" type="file" name="resume">
                <span class="file-cta">
                  <span class="file-icon">
                    <i class="fa fa-upload"></i>
                  </span>
                  <span class="file-label">
                    Escolha uma imagem
                  </span>
                </span>
              </label>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- notification -->
    <div v-if="notification.message" :class="'notification is-' + notification.type">
      <button class="delete" @click="hideNotifications"></button>{{notification.message}}
    </div>

    <!-- the form buttons -->
    <button v-if="content.state === 'saved'" type="submit" class="button is-info simple-button-margin" @click="update(true)">Atualizar e publicar</button>
    <button type="submit" class="button is-info simple-button-margin" @click="update(false)">Atualizar</button>
    <router-link :to="'/admin/content/' + $route.params.key" class="button is-danger">Cancelar</router-link>
  </div>
</template>

<script>
import firebase from 'firebase/app'
import 'firebase/storage'

import { mediaRef } from '@/admin/firebase_config'
import editorOptions from '@/admin/utils/editor-options'
import imageLoader from '@/admin/mixins/image-loader'
import notifier from '@/admin/mixins/notifier'

import Loading from "vue-loading-overlay"

export default {
  name: 'content-edit',
  components: {
    Loading
  },
  data () {
    return {
      /* Here we are filtering out the post containing the provided key in the router params
       * we are using Object.assign to copy the post by value not by reference
       * to prevent updating the post when typing */
      imagePreview: null,
      inputData: '',
      select: {
        selected:'',
        options:[]
      },
      selectOptionsRow: '',
      isLoading: false,
      fullPage: true,
      content: Object.assign(
        {},
        (this.contents.filter((c) => {
          return (c['.key'] === this.$route.params.contentKey)
        }))[0]
      ),
      editorOptions
    }
  },
  firebase: {
    media: mediaRef
  },
  props: ['contents', 'update-content', 'fields', 'categoryRoute'],
  mixins: [imageLoader, notifier],
  methods: {
    update (publish) {
      this.isLoading = true;

      if (this.content.state === 'published' && !this.content.publicationTime) {
          this.content.publicationTime = Date.now()
      }

      if (publish) {
        this.content.state = 'published'
        this.content.publicationTime = Date.now()
      }
      this.updateContent(this.content, () => {
          this.isLoading = false;
          this.$router.push({ path: "/admin/content/" + this.$route.params.key });
      })
    },
    uploadFeaturedImage (e, bdName) {
      this.isLoading = true;
      let file = e.target.files[0]
      let storageRef = firebase.storage().ref('images/' + file.name)

      storageRef.put(file).then((snapshot) => {
        snapshot.ref.getDownloadURL().then(downloadURL => {
          this.$set(this, "imagePreview", URL.createObjectURL(file))
          this.content[bdName] = downloadURL
          if (Object.values(this.media).find(e => e.path === snapshot.ref.fullPath)) return // this prevents duplicate entries in the media object
          this.$firebaseRefs.media.push({
            src: downloadURL,
            path: snapshot.ref.fullPath,
            name: snapshot.metadata.name
          })
        })
      }).then(() => {
        this.isLoading = false
      });
    },
    styleTags (fieldName) {
      if (this.inputData !== '') {
        if (!this.content[fieldName]) {
          this.content[fieldName] = []
        }
        this.inputData.split(',').forEach(tag => {
          this.content[fieldName].push(`${tag.trim()}`)
        })
        this.inputData = ''
      }
    },
    removeTag (index, fieldName, isSelect) {
       // function to remove tags and options
      // cloning object (to make Vue reactive)
      let currentContent = Object.assign({}, this.content)
      if(isSelect) {
        // delete element from select options
        currentContent[fieldName].options.splice(index, 1)
      } else {
        // delete element from arr
        currentContent[fieldName].splice(index, 1)
      }
      // return cloned object Back
      this.content = Object.assign({}, currentContent)
    },
    updateBoolContent (fieldName) {
      if (this.booleanName !== '') {
        if (!this.content[fieldName]) {
          this.content[fieldName] = ''
        }
        this.content[fieldName] = this.booleanName
      }
    },
    // function to create Select Options from input area
    styleOptions (fieldName) {
      if (this.selectOptionsRow !== '') {
        if (!this.content[fieldName]) {
          this.content[fieldName] = this.select
        }
        this.selectOptionsRow.split(',').forEach(tag => {
          this.select.options.push(`${tag.trim()}`)
        })
        this.content[fieldName].options ? [...this.content[fieldName].options, ...this.select.options] : [...this.select.options]

        this.selectOptionsRow = ''
      }
    }
    
  }
}

</script>
<style>
.tagscontainer {
  border: 2px solid #f2f2f2;
  border-radius: 5px;
  padding: 5px;
}
.pointer {
  cursor: pointer;
}

.simple-button-margin {
  margin-right: 20px;
}
</style>
