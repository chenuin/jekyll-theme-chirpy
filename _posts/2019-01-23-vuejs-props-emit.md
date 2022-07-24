---
title: "[Vue.js] 父子元件的雙向溝通示範"
date: 2019-01-23 08:00:00
categories: [Vue.js, Vue]
tags: [Vue.js, Javascript, Node.js]
---
## 第一部分、父元件→子元件
### 利用props將資料傳給components使用
#### 1. 子元件的設定
首先有一個子元件(child component)，我們設定一個 `props` 為 `userName`，型態為String，如果data內的參數，你可以直接在模板裡用 `{{ userName }}` 印出，或在function內以 `this.userName` 來進行操作。
template的內容是以顯示 `userName` 在一個HTML的輸入框裡
```javascript
// Child Component
Vue.component("child-input", {
  template: `
    <div>
      <label>Name</label>
      <input v-model="childUserName" type="text"/>
    </div>
  `,
  props: {
    // camelCase in JavaScript
    userName: String
  },
  data: function() {
    return {
      childUserName: this.userName
    };
  }
});
```
※ `Avoid mutating a prop directly since the value will be overwritten whenever the parent component re-renders. Instead, use a data or computed property based on the prop's value.` 代表Vue會警告你盡量不要直接修改props參數的值，因此我們設定了childUserName來避免這個問題。

#### 2. 父元件的設定
在HTML裡要用user-name來傳值，也就是說component的命名法遵循 `camelCase` ，到HTML內時則是用 `kebab-case` 來識別。
`user-name`和 `:user-name` 兩者的用法不同，如果是 `user-name="initial_input"`，會把 "initial_input" 當成字串傳過去，如果前面加上冒號「:」變成 `:user-name="inital_input"`，則是把 `inital_input` 這個參數的值傳過去。
```vue
<!-- HTML -->
<div id="app">
  <!-- kebab-case in HTML -->
  <child-input :user-name="initial_input"></child-input>
</div>
```
```javascript
// js
new Vue({
  el: "#app",
  data: function() {
    return {
      initial_input: "Annie",
    };
  }
});
```


## 第二部分、子元件→父元件
### 利用emit將components的資料回傳
#### 1. 子元件的設定
延續第一部分，我們在methods內新增一個 `sendToParent` 的function，`@input="sendToParent"` 代表我們觸發的時機。
看一下 `sendToParent` 內容，`$emit` 後面第一個參數"`update-text`"，代表設定一個 `update-text` 的事件，第二參數是同時把 `childUserName` 這個參數傳出去，如果需要傳更多的參數，直接用逗號分隔接在後面。
```javascript
Vue.component("child-input", {
  template: `
    <div class="form-group mt-3">
      <label>Name</label>
      <input v-model="childUserName" @input="sendToParent" type="text"/>
    </div>
  `,
  props: {
    userName: String
  },
  data: function() {
    return {
      childUserName: this.userName
    };
  },
  methods: {
    sendToParent: function() {
      this.$emit("update-text", this.childUserName);
    }
  }
});
```

#### 2. 父元件的設定
父元件的部分也新增一個 `getChildText` 的function來接收子元件的資料，當子元件觸發'`update-text`'時，就會執行 `getChildText` 來接收傳送的值，value就是從子元件傳來的 `childUserName`。如果傳多個值，記得在這邊填相應數量的參數來接收。
```javascript
// JS
new Vue({
  el: "#app",
  data: function() {
    return {
      initial_input: "Annie",
    };
  },
  methods: {
    getChildText: function(value) {
      this.initial_input = value;
    }
  }
});
```
```vue
<!-- HTML -->
<div id="app">
  <child-input :user-name="initial_input" 
               @update-text="getChildText"></child-input>
</div>
```


完整的程式碼請參考codepen上的範例：[Sending Messages between Parent and Child](https://codepen.io/chenuin/pen/LqEvyM)

相關文章：
 - [[Vue.js] 如何在component自訂v-model](https://chenuin.blogspot.com/2019/05/vue-component-v-model.html)
