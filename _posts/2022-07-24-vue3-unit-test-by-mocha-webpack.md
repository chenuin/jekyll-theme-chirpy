---
title: mocha + webpack 的 Vue3 元件單元測試
date:   2022-07-24 08:00:00
categories: [Unit Test, Mocha, Vue]
tags: [Node.js, Javascript, Vue.js, Vue3, Mocha, Webpack5, Unit Test, Chai]
---
先說結論，我認為**不適合**用 mocha 進行 vue3 單元測試([@vue/test-utils](https://test-utils.vuejs.org/))，反覆查了很久的資料，相關的套件支援度不足等有重重的障礙，根據 @vue/test-utils 目前提供的測試範例，選擇 [Vitest](https://vitest.dev/) 會更適合。

> 完整程式碼上傳至 Github ([連結](https://github.com/chenuin/vue3-webpack5-template/tree/todomvc-with-mocha-testing))。
{: .prompt-info }

## 一、安裝
首先，第一個問題就是 vue 的版本不能太新，目前只支援 `3.0.7`，因此對應安裝了相同版本的 `@vue/server-renderer`
```bash
npm install --save-dev @vue/server-renderer@3.0.7
```

再來請安裝 webpck 和 mocha，mochapack，[mochapack](https://github.com/sysgears/mochapack) 是用來讀取 webpack 設定將元件 render 出來的套件，可支援 webpack5 和 mocha 9
```bash
npm install --save-dev webpack mocha mochapack
```

其他有兩個類似的套件：
- [**mocha-webpack**](https://github.com/zinserjan/mocha-webpack):
  - mocha: '>=4 <=5'
  - webpack: '^4.0.0'
- [**instant-mocha**](https://github.com/privatenumber/instant-mocha):
  - mocha: '^6.1.4 \|\| ^8.3.2'
  - webpack: '^4.40.0 \|\| ^5.0.0'

mochapack 是由 mocha-webpack 延伸而來的，三者用法都非常接近，但上述兩個對 webpack 和 mocha 版本的支援度都比 mochapack 差

再來請安裝 vue3 官方的元件測試套件 @vue/test-utils
```bash
npm install --save-dev @vue/test-utils
```
mocha 不像是 jest 已經內建支援 jsdom、assertion ，所以要另外安裝
```bash
npm install --save-dev webpack-node-externals jsdom-global
npm install --save-dev expect
```

## 二、設定
新增測試專用的 webpack 設定檔 `webpack.config-test.js`

```js
const nodeExternals = require('webpack-node-externals');
const { VueLoaderPlugin } = require('vue-loader');

module.exports = {
    mode: 'development',
    target: 'node',  // webpack should compile node compatible code
    externals: [nodeExternals()], // in order to ignore all modules in node_modules folder
    devtool: 'inline-cheap-module-source-map',
    module: {
        rules: [
            {
                test: /\.vue$/,
                loader: 'vue-loader',
            },
            {
                test: /\.css$/i,
                use: ['style-loader', 'css-loader'],
            },
        ],
    },
    plugins: [
        new VueLoaderPlugin(),
    ],
};
```
{: file='webpack.config-test.js'}

設定 jsdom 設定檔 src/tests/setup.js
```js
require('jsdom-global')();
```
{: file='src/tests/setup.js'}

最後，請到 `package.json` 設定 script 指令
```json
{
  "scripts": {
    "test": "npx mochapack --webpack-config webpack.config-test.js --require src/tests/setup.js src/tests/**/*.spec.js",
  }
}
```
>**Usage**: mochapack [options] [<file|directory|glob> ...]<br>
<br>
**Options**<br>
　--webpack-config　　path to webpack-config file<br>
　--require, -r　　　　require the given module
{: .prompt-tip }

## 三、執行
新增測試(範例請參考 [Counter.spec.js](https://github.com/chenuin/vue3-webpack5-template/blob/todomvc-with-mocha-testing/src/tests/components/Counter.spec.js))後,執行指令即可
npm run test


## 四、限制

### 無法支援最新 vue3

無法支援目前 vue 最新版本 3.2.37，發現底下錯誤訊息
>ReferenceError: SVGElement is not defined
{: .prompt-danger }

### 無法支援 SFC

無法支援 SFC ，發現底下警告訊息:
>\[Vue warn\]: Component is missing template or render function.
{: .prompt-warning }


## 參考文件
- [用 Mocha 和 webpack 測試單文件組件](https://v1.test-utils.vuejs.org/zh/installation/#%E7%94%A8-mocha-%E5%92%8C-webpack-%E6%B5%8B%E8%AF%95%E5%8D%95%E6%96%87%E4%BB%B6%E7%BB%84%E4%BB%B6)
- [mochapack 文件](https://sysgears.github.io/mochapack/)
- [taro新建vue3项目后，编译成小程序端报错ReferenceError: SVGElement is not defined](https://blog.csdn.net/weixin_56436531/article/details/115343623)
