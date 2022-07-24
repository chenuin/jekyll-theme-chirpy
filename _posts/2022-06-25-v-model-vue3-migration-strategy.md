---
title: 【升版指南】Vue3 非兼容的特性 v-model 跟 Vue2 完全不一樣
date:   2022-06-25 08:00:00
categories: [Vue.js, Vue]
tags: [Node.js, Javascript, Vue.js, Vue3, Migration Strategy]
---
## Vue2 與 Vue3 比較
vue2 v-model 的使用方式在這篇「[[Vue.js] 如何在component自訂v-model](https://chenuin.blogspot.com/2019/05/vue-component-v-model.html)」介紹，簡單說就是結合`v-model`其實綁定了名為 `value` 的 `props` 和 `input` 的 `emit`事件，因此所謂的 `v-model` 就是一個父子元件的雙向溝通，在 vue2 兩者是相同的：
```vue
<input v-model="username">

<input :value="username"
       @input="(value)=>(username=value)">
```
<br>
**但是！**
vue3 `props` 預設名稱從 `value` 改為 `modelValue` ，`emit` 名稱則從 `input` 改為 `update:modelValue`，如果用錯了就無法達成雙向的綁定。
```vue
<input v-model="username">

<input :model-value="username"
       @update:modelValue="(value)=>(username=value)">
```

在 vue2 中可以用自訂 `model` 來自訂 `props` 和 `emit` 名稱，在 Vue3 移除不再支援，如果不想使用 `modelValue` 的話，在 Vue3 可以這樣寫：
```vue
<MyComponent v-model:username="username" />

<MyComponent
       :username="username"
       @update:username="(value)=>(username=value)" />
```
元件本身應該增加這個 `props`,更新時會呼叫 `update:username`
```
props: {
    username: {
        type: String,
        required: true,
    },
}
```
{: file='MyComponent.vue'}

跟 vue2 `.sync` 有點相似，vue3不再支援 `.sync` ，寫法會是 `v-model:[NAME]` 後面 Name 就是想自訂的名稱，更新時 `update:[NAME]`，以事件名稱來看不算是完全客製，須按照規則觸發向上更新

但這個特性最棒的是支援多組 `v-model` ，你可以這樣寫：
```vue
<MyComponent
       v-model:username="username"
       v-model:password="password" />
```

## 重點整理

總結將 vue2 升至 vue3，v-model 應該注意三個地方：

### 不再使用 .sync
```vue
<MyComponent :username.sync="name" />

<!-- to be replaced with -->

<MyComponent v-model:username="name" />
```

### 將所有的 value 和 input 和 modelValue 和 update:modelValue
```vue
<MyComponent v-model="name" />
```
```js
export default {
  props: {
    modelValue: String, // "vaule" is replaced
  },
  emits: ['update:modelValue'],
  methods: {
    changeName(name) {
      this.$emit('update:modelValue', name);  // "input" is replaced
    },
  },
};
```

### 不再使用 model
直接用 `v-model`、`v-model:[Name]`

參考文章: [https://v3-migration.vuejs.org/breaking-changes/v-model.html](https://v3-migration.vuejs.org/breaking-changes/v-model.html)
