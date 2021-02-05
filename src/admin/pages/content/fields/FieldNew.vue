<template>
  <div class="is-widescreen">

    <h3 class="is-size-3 has-text-centered">Criar campo de conteúdo</h3><br>
    <div class="box">
      <div class="columns">
        <div class="column is-one-third">
          <div class="field">
            <label class="label">Nome do campo</label>
            <div class="control">
              <input class="input" type="text" placeholder="Nome do campo" v-model="fieldName">
            </div>
          </div>
          <div class="field">
            <label class="label">Nome no banco de dados</label>
            <div class="control">
              <input class="input" type="text" placeholder="Nome no banco de dados" v-model="fieldBDName">
            </div>
          </div>
          <div class="field">
            <label class="label">Tipo do campo</label>
            <div class="control">
              <dropdown :options="fieldTypes" :selectedElement="fieldType" />
            </div>
          </div>
          <div class="field">
            <div class="control inline">
              <label class="label">Permitir multi-seleção</label>
              <checkbox v-model="multiValue" />
            </div>
            <div class="control inline">
              <label class="label">Listar campo</label>
              <checkbox v-model="listable" />
            </div>
          </div>
        </div>
      </div>
      <div class="buttons">
        <button type="submit" class="button is-info" :disabled="fieldName === '' || fieldBDName === '' || fieldType.id === ''" @click="add()">Adicionar Campo</button>
        <router-link to="/admin/content" class="button is-danger">Cancelar</router-link>
      </div>
    </div>
  </div>
</template>

<script>
import checkbox from '@/admin/components/shared/Checkbox'
import dropdown from '@/admin/components/shared/Dropdown'
import { contentsRef, fieldsRef } from '@/admin/firebase_config'
import notifier from '@/admin/mixins/notifier'
export default {
  name: 'fieldNew',
  mixins: [notifier],
  firebase: {
    contents: contentsRef,
    fields: fieldsRef
  },
  data () {
    return {
      fieldName: '',
      fieldBDName: '',
      fieldType: {
        id: '',
        label: 'Tipo do campo'
      },
      multiValue: false,
      listable: false,
      fieldTypes: [
        {
          id: 'textarea',
          label: 'Textarea'
        },
        {
          id: 'textbox',
          label: 'Textbox'
        },
        {
          id: 'integer',
          label: 'Integer'
        },
        {
          id: 'boolean',
          label: 'Boolean'
        },
        {
          id: 'url',
          label: 'Url'
        },
        {
          id: 'richtextbox',
          label: 'Richtextbox'
        },
        {
          id: 'tags',
          label: 'Tags'
        },
        {
          id: 'select',
          label: 'Select'
        },
        {
          id: 'image',
          label: 'Image'
        }
      ]
    }
  },
  props: ['add-field'],
  methods: {
    add () {
      let f = {
        name: this.fieldName,
        bdName: this.fieldBDName,
        type: this.fieldType.id,
        multiValue: this.multiValue,
        listable: this.listable
      }
      if (f.listable && f.type === 'textarea') {
        this.$notify({
          title: 'Important message',
          text: 'Field type must be other than \'textarea\' to apply sorting',
          type: 'error'
        })
        return
      }
      this.addField(f)
      this.fieldName = ''
      this.fieldBDName = ''
      this.fieldType = {
        id: '',
        label: 'Field type'
      }
      this.multiValue = false
      this.listable = false
      this.$router.push({ path: '/admin/content' })
    },
    resetForm () {
      this.name = ''

      for (var key in this.supports) {
        this.supports[key] = false
      }
    }
  },
  components: {
    checkbox,
    dropdown
  }
}

</script>

<style>

.control.inline {
    display: flex;
}

.control.inline input {
    margin: 7px 0 0 10px;
}

</style>
