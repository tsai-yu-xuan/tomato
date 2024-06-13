<template>
  <v-container>
    <v-row>
      <v-col cols="12">
        <h1 class="text-center">代辦事項</h1>
      </v-col>
      <v-col cols="12">
        <v-text-field
          variant="outlined"
          label="新增事項"
          clearable
          append-icon="mdi-plus"
          @keydown.enter="onInputSubmit"
          @click:append="onInputSubmit"
          v-model="newItem"
          :rules="[rules.required, rules.length]"
          ref="newItemTextField"
        ></v-text-field>
        <v-table>
          <thead>
            <tr>
              <th>名稱</th>
              <th>操作</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, i) in items" :key="item.id">
              <td>
                <span v-show="!item.edit">{{ item.name }}</span>
                <v-text-field
                  v-show="item.edit"
                  v-model="item.model"
                  :rules="[rules.required, rules.length]"
                  autofocus
                  @keydown.enter="onEditInputSubmit(item.id, i)"
                  ref="editItemTextField"
                ></v-text-field>
              </td>
              <td>
                <template v-if="!item.edit">
                  <v-btn icon="mdi-pencil" @click="editItem(item.id)"></v-btn>
                  <v-btn icon="mdi-delete" @click="delItem(item.id)"></v-btn>
                </template>
                <template v-else>
                  <v-btn icon="mdi-check" @click="onEditInputSubmit(item.id, i)"></v-btn>
                  <v-btn icon="mdi-undo" @click="cancelEditItem(item.id)"></v-btn>
                </template>
              </td>
            </tr>
          </tbody>
        </v-table>
      </v-col>
      <v-col cols="12">
        <h1 class="text-center">完成事項</h1>
      </v-col>
      <v-col cols="12">
        <v-table>
          <thead>
            <tr>
              <th>名稱</th>
              <th>操作</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="item in finishedItems" :key="item.id">
              <td>{{ item.name }}</td>
              <td>
                <v-btn icon="mdi-delete" @click="delFinishItem(item.id)"></v-btn>
              </td>
            </tr>
          </tbody>
        </v-table>
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup>
import { useListStore } from '@/stores/list'
import { ref } from 'vue'
import { storeToRefs } from 'pinia'
import { definePage } from 'vue-router/auto'

definePage({
  meta: {
    title: '番茄鐘 | 清單'
  }
})

const list = useListStore()
const { addItem, editItem, delItem, cancelEditItem, confirmEditItem, delFinishItem } = list
const { items, finishedItems } = storeToRefs(list)

const newItem = ref('')
const newItemTextField = ref(null)
const editItemTextField = ref([])

const rules = {
  required: (value) => {
    return Boolean(value) || '欄位必填'
  },
  length: (value) => {
    return value.length >= 3 || '必須三個字以上'
  }
}

const onInputSubmit = async () => {
  const validate = await newItemTextField.value.validate()
  console.log(validate)
  if (validate.length > 0) return
  addItem(newItem.value)
  newItemTextField.value.reset()
}

const onEditInputSubmit = async (id, i) => {
  const validate = await editItemTextField.value[i].validate()
  if (validate.length > 0) return
  confirmEditItem(id)
}
</script>
