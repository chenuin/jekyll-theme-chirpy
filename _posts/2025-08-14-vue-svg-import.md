---
title: "[Vue.js] SVG 引入與 Typescript 型別設定"
date:   2025-08-14 08:00:00
categories: [Vue.js, Vue, Vite]
tags: [Node.js, Javascript, Vue.js, Vue3, Vite, Typescript, SVG]
---

## ❌錯誤示範

如何在vue專案裡使用 svg 檔案，最直覺的做法：
```vue
<template>
    <svg>
        <use href="./assets/penguin-svgrepo-com.svg" width="50%" height="50%" />
    </svg>
</template>
```
{: file='App.vue'}

開發階段只要注意路徑正確，就能正常顯示。

但編譯到正式環境時，這段 `<use>` 標籤使用了 `href="data:image/svg+xml,...` 來嵌入 SVG 圖片，但這種用法在 `<use>` 上是無效的，因此圖示不會正確顯示。

改用圖片 `<img>` 來處理或許可以解決：
```vue
<img src="data:image/svg+xml,<svg ...>..." />
```
雖然這樣瀏覽器就會把它當成圖片正常顯示，但無法用 CSS 改 `fill` 或 `stroke`，可調整性較差。

## ✔️解決方法

### 1. 安裝 vite-svg-loader 及設定

```bash
npm install -D vite-svg-loader
```
接著，`vite.config.ts`也要增加設定：
```ts
import svgLoader from 'vite-svg-loader';

export default {
    plugins: [
        svgLoader(),
    ],
    // ...
};
```
{: file='vite.config.ts'}


### 2. 新增型別宣告

> 假設專案並不是使用 Typescript，可以省略這個步驟。

新增 `vite-env.d.ts`，其主要用途是為 Vite 專案提供型別宣告。它能確保你的 TypeScript 專案能夠正確識別和處理 Vite 特有的功能，而不會報錯。

簡單來說，它就像一個翻譯官，告訴 TypeScript 編譯器：「嘿，Vite 會處理這些特殊檔案，請不要報錯，它們的型別是長這樣。」
```ts
// src/vite-env.d.ts
declare module '*.svg' {
    const content: string

    export default content
}
```
你也可以使用 `env.d.ts`。但 `vite-env.d.ts` 的命名方式更明確地表示它是為 Vite 專案服務的。

建議放在 `src/` 資料夾的根目錄，Vite 會自動尋找資料夾中的 `env.d.ts` 或 `vite-env.d.ts` 檔案，並將其視為專案的型別宣告。

如果你將它放在其他位置，你需要在 `tsconfig.json` 的 `include` 或 files 欄位中手動指定它的路徑，確保 TypeScript 編譯器能夠找到它。


### 3. 使用 svg
最後只要調整引用的方法，可以把 svg 當作一個 component 來使用。

```vue
<template>
    <Penguin />
</template>

<script setup lang="ts">
import Penguin from '@/assets/penguin.svg'
</script>
```
{: file='App.vue'}

這樣一來原本支援的 attr 都可以使用，例如：

```vue
<Penguin width="200" height="200" />
```

參考資料：
- [https://www.npmjs.com/package/vite-svg-loader](https://www.npmjs.com/package/vite-svg-loader)