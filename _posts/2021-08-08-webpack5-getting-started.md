---
title: "webpack5 安裝及基礎教學"
date: 2021-08-08 08:00:00
categories: [Vue.js, Webpack]
tags: [安裝教學, Webpcak, Webpack5, Node.js, HTML, Javascript, Linux]
---
根據維基百科：「Webpack 是一個開源的前端打包工具。Webpack 提供了前端開發缺乏的模組化開發方式，將各種靜態資源視為模組，並從它生成優化過的程式碼。」，使用前必須安裝 Node.js。

webpack 從版本4.0.0開始，可以不需要設定設定檔(`webpack.config.js`)，設定檔最基本的設定分別是：`Entry`、`Output`，前者代表 webpack 必須從哪邊開始進行打包，後者則是定義封裝輸出的路徑以及檔案名稱。


以下參考官網簡單的範例，執行前請先安裝 `Node.js`，可以參考「[安裝node js](https://chenuin.blogspot.com/2018/07/node-js-v894.html)」替換成需要的版本。

### 一、安裝
請先新增一個專案目錄，並安裝 `webpack`、`webpack-cli`
```bash
mkdir webpack-demo
cd webpack-demo>
cd webpack-demo>
npm init -y>
npm install webpack webpack-cli --save-dev
```
此時目錄內應該會多一個檔案 `package.json` 和目錄 `node_module/`。


### 二、新增Entry module
新增目錄 `src/`，以及檔案 `index.js`
```javascript
const element = document.createElement('div');

element.innerHTML = 'Hello World!';

document.body.appendChild(component());
```


### 三、新增Output
新增目錄 `dist/`，以及檔案 `index.html`
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Getting Started</title>
  </head>
  <body>
    <script src="main.js"></script>
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
    └── index.js
```


### 四、執行
執行下面的指令開始打包
```bash
npx webpack
```

完成後會發現目錄 `dist/` 下多了一個檔案 `main.js`，此時可以開啟 `index.html` ，看到 Hello World! 就代表打包成功囉！



在沒有設定檔的情況下，webpack預設會尋找檔名 `src` 或是目錄 `src/` 底下的 index，並將打包好的檔案放到目錄 `dist/` 底下，預設檔名是 `main.js`，等同於：
```javascript
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'main.js',
    path: path.resolve(__dirname, 'dist'),
  },
;
```

可以透過指令來指定設定檔的路徑及名稱：
```bash
npx webpack --config webpack.config.js
```


範例還未使用`Loader`、`Plugin`等功能，在這個範例還未使用 css/image，也可以透過 Loader設定處理，另外手動新增 `index.html` 也可以透過相關的 Plugin 自動產生。

其他進階設定的功能可以參考 [https://webpack.js.org/guides/getting-started/](https://webpack.js.org/guides/getting-started/) 了解更多。

