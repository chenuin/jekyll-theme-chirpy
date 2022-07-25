---
title: "Vue3.2 &lt;script setup&gt; 實作 TodoMVC"
date:   2022-07-25 08:00:00
categories: [Vue.js, Vue]
tags: [Vue.js, Vue3, Node.js, Javascript]
---
Vue3.2開始將 `<script setup>` 移除experimental status，和`setup()`區別在有許多 option API 有了替代方案，`props`, `emit` 也可以寫在 `setup`，variable 或 function 也不需要透過 return 才能讓 `<template>` 使用，哇！寫法怎麼好像有點既是感呢

下面會利用 TodoMVC 的練習，比較與統整 `<script setup>` 和 `setup()` 常用的方法的差異。

> TodoMVC 完整程式碼上傳至 Github ([連結](https://github.com/chenuin/vue3-webpack5-template/tree/4fb0a6ca98236d037c3f5e15e29259834a67dcf4))。
{: .prompt-info }

## data
### setup()
```vue
<script>
import { ref } from 'vue';

export default {
    setup() {
        const newTodo = ref(undefined);

        return {
            newTodo,
        };
    },
}
</script>
```
{: file='Before'}

### &lt;script setup&gt;
宣告 ref 沒有差異，差在需不需要 return
```vue
<script setup>
import { ref } from 'vue';

const newTodo = ref(undefined);
</script>
```
{: file='After'}

## component
```vue
<template>
    <TodoListEditor />
</template>
```

### setup()
```vue
<script>
import TodoListEditor from 'components/TodoListEditor.vue';

export default {
    components: {
        TodoListEditor,
    },
}
</script>
```
{: file='Before'}

### &lt;script setup&gt;
import 之後就可以直接在 `<template>` 使用
```vue
<script setup>
import TodoListEditor from 'components/TodoListEditor.vue';
</script>
```
{: file='After'}

## props
### setup()
```vue
<script>
export default {
    props: {
        todoList: {
            required: true,
            type: Array,
        },
    },
}
</script>
```
{: file='Before'}

### &lt;script setup&gt;
`defineProps` 裡面內容與之前 `props` 相同
```vue
<script setup>
const props = defineProps({
    todoList: {
        required: true,
        type: Array,
    },
});
</script>
```

## emit
### setup()
```vue
<script>
export default {
    emits: ['remove:todo', 'update:todo'],
    setupt(props, {emit}) {
        function removeTodoItem(id){
            emit('remove:todo', id);
        }
    },
}
</script>
```
{: file='Before'}

### &lt;script setup&gt;
`defineEmits` 裡面內容與之前 `emits` 相同
```vue
<script setup>
const emit = defineEmits(['remove:todo', 'update:todo']);

function removeTodoItem(id){
    emit('remove:todo', id);
}
</script>
```
{: file='After'}

## directive
directive是所有寫法中我最不適應的，底下是頁面載入時可以有 autofocus 的效果，可以根據不同 lifecyle 定義，利如 mouted
```vue
<input
    v-model="newTodo"
    v-focus>
```

### setup()
```vue
<script>
const focus = {
    mounted: el => el.focus(),
};

export default {
    directives: {
        focus,
    },
}
</script>
```
{: file='Before'}

### &lt;script setup&gt;
```vue
<script setup>
const vFocus = {
    mounted: el => el.focus(),
};
</script>
```
{: file='After'}

## lifecyle
基本上沒有什麼區別
### setup()
```vue
<script>
import {onMounted} from 'vue';

export default {
    setup() {
        onMounted(() => {
            // to something
        });
    },
}
</script>
```
{: file='Before'}

### &lt;script setup&gt;
```vue
<script setup>
import {onMounted} from 'vue';

onMounted(() => {
    // to something
});
</script>
```
{: file='After'}
