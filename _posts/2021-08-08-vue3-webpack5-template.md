---
title: "[Vue.js] 如何建立 Vue3 + webpack5 專案範例"
date:   2021-08-08 08:00:00
categories: [Vue.js, Vue, Webpack]
tags: [Vue.js, Vue3, Webpcak, Webpack5, Node.js, HTML, Javascript, Linux]
---
以下說明如何使用 webpack5 打包 Vue3 專案，若尚未安裝 webpack 5，可以參考「(webpack5 安裝及基礎教學)[https://chenuin.github.io/webpack/2021/08/08/webpack5-getting-started.html]」。

### 一、安裝
需要安裝的套件如下，特別需要注意的是，在 Vue3 裡支援副檔名 `.vue` 的 single-file components 的套件，從 `vue-template-compiler` 變成 `@vue/compiler-sfc`：
- vue
- @vue/compiler-sfc
- vue-loader
- webpack
- webpack-cli

請先新增專案，並在目錄內新增 `package.json`，並 npm 執行安裝。
```json
{
  "name": "vue3-webpack5-template",
  "version": "1.0.0",
  "description": "vue3-webpack5-template",
  "private": true,
  "scripts": {
    "build": "webpack"
  },
  "author": "chenuin",
  "license": "ISC",
  "devDependencies": {
    "@vue/compiler-sfc": "^3.1.5",
    "vue-loader": "^16.1.2",
    "webpack": "^5.49.0",
    "webpack-cli": "^4.7.2"
  },
  "dependencies": {
    "vue": "^3.1.5"
  }
}
```
執行指令安裝：
```bash
npm install
```


### 二、設定打包的設定檔
首先針對 Vue 專案所設定的，Loader 可分為`test`、`loader`，前者是定義哪些檔案需要處理，像這裡就是副檔名為 `.vue `的檔案；後者則是使用哪一個套件處理 ：
```
module: {
    rules: [
        {
            test: /\.vue$/,
            loader: 'vue-loader'
        },
    ],
},
plugins: [
    new VueLoaderPlugin(),
],
```
當專案內import vue 可以使用別名(alias)： 
```
resolve: {
    alias: {
        vue: 'vue/dist/vue.esm-bundler.js',
    },
},
```

因此完整的 `webpack.config.js` 如下：
```javascript
const path = require('path');
const { VueLoaderPlugin } = require('vue-loader');

module.exports = {
    entry: './src/index.js',
    output: {
        filename: 'main.js',
        path: path.resolve(__dirname, 'dist'),
    },
    module: {
        rules: [
            {
                test: /\.vue$/,
                loader: 'vue-loader'
            },
        ],
    },
    resolve: {
        alias: {
            vue: 'vue/dist/vue.esm-bundler.js',
        },
    },
    plugins: [
        new VueLoaderPlugin(),
    ],
};

```


### 三、新增Entry, Ouput等內容
在目錄內 `src/` 新增兩個檔案:
Vue3 主要元件 `App.vue`
```vue
<template>
  <div class="app">
    <p
      v-text="msg" />
  </div>
</template>

<script>
export default {
    name: 'App',
    setup() {
      return {
        msg: 'Hello World!',
      };
    },
};
</script>
```

新增 Entry File: `index.js`
設定別名的用途可以看第一行 `import App from './App.vue';`。
```javascript
import { createApp } from 'vue';
import App from './App.vue';

createApp(App)
    .mount('#app');
```

目錄 `dist/` 則是新增 `index.html`，要注意的是先前 `index.js` 是綁定到 `#app`，所以 `<div id="app"></div>` 就是 vue render的地方。

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8" />
        <title>Getting Started</title>
    </head>
    <body>
        <div id="app"></div>
        <script src="./main.js"></script>
    </body>
</html>

```

此時的專案架構應該會像是：
```
webpack-demo
├── package.json
├── dist
│   └── index.html
└── src
    ├── App.vue
    └── index.js
```
專案已經上傳 Github [vue3-webpack5-template](https://github.com/chenuin/vue3-webpack5-template)，可以下載使用。


### 四、執行
執行下面的指令開始打包
```bash
npx webpack
```

完成後目錄 `dist/` 下多了一個檔案 `main.js`，此時可以開啟 `index.html` ，看到 Hello World! 就代表打包成功囉！

