---
title: "[Vue.js] v-for設定key的作用與影響"
date:   2019-06-16 08:00:00
categories: [Vue.js, Vue]
tags: [Vue.js, Javascript, Node.js]
---
v-for迭代陣列或物件時需要設定key，是為了避免重複產生DOM元素而浪費資源，因此將key視為一個辨識的依據，所有的key必須保持唯一。因此如果key值不小心重複，console甚至會出現 `Duplicate keys detected: ... This may cause an update error.` 這樣的警示訊息。

為了顯示key的作用和影響，用下面的例子來看，程式內容大意是根據 `menu` 的內容產生多個 `product-box` ，兩個唯一的差別是前著用 `index` 當 key ，後者則是用 `id` 當作 key，此外多加一個 button 來更動資料的內容。
```vue
<template>
  <div>
    <product-box v-for="item,index in menu" :key="index" :value="item.id">
      {{ item.name }}
    </product-box>

    <product-box v-for="item in menu" :key="item.id" :value="item.id">
      {{ item.name }}
    </product-box>

    <button @click="addFirstElement">Add</button>
  </div>
</template>

<script>
import ProductBox from './ProductBox'
export default {
  data () {
    return {
      menu: [
        {id: 'A001', name: 'milk tea'},
        {id: 'A002', name: 'juice'}
      ]
    }
  },
  methods: {
    addFirstElement: function() {
      let first_elm = [{id: 'A000', name: 'coffee'}];
      this.menu = first_elm.concat(this.menu);
    }
  },
  components: {
    ProductBox
  }
}
</script>
```

ProductBox.vue
```vue
<template>
  <div>
    {{ dispay_text }} <slot></slot>
  </div>
</template>

<script>
  
  export default {
    data: function() {
      return {
        dispay_text: ''
      }
    },
    props: {
      'value': String
    },
    created: function() {
      this.dispay_text = this.value;
    }
  }
</script>
```

初始的情況下：
```
A001 milk tea
A002 juice
```

接著，點button在開頭插入一個新的資料，預想的顯示如下，如果用id當作key的話也是產生一樣的結果。
```
// key=id

A000 coffee
A001 milk tea
A002 juice
```

這時因為用index當作key，所以`原本index 1和2因為沒有偵測到變化並不會重新產生，也就是不會重新經歷created`，所以出現所謂的 `update error` ，結果會變成：
```
// key=index

A001 coffee
A002 milk tea
A002 juice
```

雖然用index當作key很方便，但並非所有的情況都適合使用index當作key。
