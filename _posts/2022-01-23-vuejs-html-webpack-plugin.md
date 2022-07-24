---
title: webpack 常用 plugin 介紹 - HtmlWebpackPlugin 自動產生 Html
date:   2022-01-23 08:00:00
categories: [Vue.js, Vue, Webpack]
tags: [安裝教學, Webpcak, Webpack5, Node.js, HTML, Javascript, Linux, Vue.js, Vue3, HtmlWebpackPlugin, Plugins]
---
根據「[[Vue.js] 如何建立 Vue3 + webpack5 專案範例]({% post_url 2021-08-08-vue3-webpack5-template %})」的內容，封裝時已經先建立目錄 `dist/` ，新增 `index.html`，預先將 `main.js` 加到 `script` 中。

接著，以下要介紹 `HtmlWebpackPlugin` ，這個 webpack plugin 可以自動產生 Html，並自動將所有的 js 檔加入 script 中。下面的操作會用 [vue3-webpack5-template](https://github.com/chenuin/vue3-webpack5-template) 這個專案操作，可以先 clone下來並 npm install。

### 一、安裝
```bash
npm install html-webpack-plugin --save-dev
```

### 二、新增模板
新增 `index.html` ，並保留一些區域方便我們可以自訂。
```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8"/>
        <title>
            <%= htmlWebpackPlugin.options.title %>
        </title>
    </head>
    <body>
        <div id="<%= id %>"></div>
    </body>
</html>
```

### 三、新增 Webpack 設定
為了突顯 HtmlWebpackPlugin 功能的強大，首先刻意將輸出的 js 檔案名稱改為 `[name].[contenthash].js`，這時變得無法預期編譯後的檔案名稱。

再來將 HtmlWebpackPlugin 加入 plugin，完整檔案請點[連結](https://github.com/chenuin/vue3-webpack5-template/blob/f600dd36480afa13bf0438e9f3dc56da7d3bcb3f/webpack.config.js)

```javascript
// webpack.config.js
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  output: {
    filename: '[name].[contenthash].js',
    path: path.resolve(__dirname, 'dist'),
  },

  ...

  plugins: [
    new HtmlWebpackPlugin({
      template: 'template/index.html',
      title: 'Getting Started',
      templateParameters: {
        id: 'app',
      },
    }),
  ],
};
```


上面共使用了三個參數：
 - `template` - 顧名思義是模板的路徑
 - `title` - html 檔案的 `<title>`

也說明為什麼在 `index.html` 有這一段
```
<%= htmlWebpackPlugin.options.title %>
```
 - `templateParameters` - 這裡傳入一個物件，將 id 訂為 app，因此模板這段程式碼，將會使用這裡的設定複寫

```
<div id="<%= id %>"></div>
```

若要了解更多參數的使用，可以參考[文件](https://github.com/jantimon/html-webpack-plugin#options)。

### 四、執行
執行下面的指令開始打包
```bash
npm run build
```

程式碼已經 push 到分支 `test-webpack-plugin`，或者參考變更可以按[這裡](https://github.com/chenuin/vue3-webpack5-template/compare/main...test-webpack-plugin)。
