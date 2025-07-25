---
title: "[React] JSX 常見語法雷區"
date:   2025-07-25 08:00:00
categories: [React, JSX]
tags: [React, Javascript, JSX]
---

### 1. JSX 中 HTML 標籤必須自閉合
HTML 中 input 是 void element，本來不需要閉合標籤。
```jsx
// ✅ 正確
<input type="text" />

// ❌ 錯誤
<input type="text">  // JSX 會報錯
```


### 2. class 要改寫成 `className`
JSX 是 JavaScript，不允許使用 JS 關鍵字 `class`。
```jsx
// ✅ 正確
<div className="box" />

// ❌ 錯誤
<div class="box" />  // React 不認得
```


### 3. `for` 要改寫成 `htmlFor`
表單 label 的 `for` 在 JSX 中是保留字，要改成 `htmlFor`：
```jsx
<label htmlFor="email">Email</label>
```


### 4. JS 表達式要放在 `{}` 裡
Vue.js template 則使用是雙括號 `{{}}`
```jsx
const name = "Annie";

// ✅ 正確
<p>Hello, {name}</p>
<h1>{name.toUpperCase()}</h1>

// ❌ 錯誤
<p>Hello, {{name}}</p>
```


### 5. style 需用物件寫法，且值要加引號或轉成 string
style 物件的 key 是 camelCase（如: `fontSize`），不同於 HTML/CSS 的 kebab-case（如: `font-size`）
```jsx
// ✅ 正確
<div style={{ color: 'red', fontSize: '16px' }} />

// ❌ 錯誤
<div style="color: red; font-size: 16px;" />  // HTML 寫法不支援
```

雙括號讓解讀困難，因此也把物件先儲存到一個變數中再傳給 `style`，效果完全一樣。
```js
const config = { color: 'red', fontSize: '16px' };
const element = <div style={config} />
```


### 6. JSX 裡只能回傳單一根元素
必須使用 Fragment
```jsx
// ✅ 正確（使用 <></> Fragment）
return (
  <>
    <Header />
    <Content />
  </>
);

// ❌ 錯誤（JSX 中不能 return 兩個平行元素）
return (
  <Header />
  <Content />
);
```


### 7. 事件寫法是駝峰命名，值是函式
非常容易與 HTML 原本的寫法混淆
```jsx
// ✅ 正確
<button onClick={handleClick}>Click</button>

// ❌ 錯誤
<button onclick="handleClick()">Click</button>
```


### 8. 條件渲染
在 JSX 中，沒有像 Vue.js 的 `v-if` 那樣的指令語法，但可以用 JavaScript 表達式來實現相同的功能，或是三元運算或邏輯 (`&&`) 來達到條件渲染。
```jsx
{isLoading && <Loading />}
{isLoggedIn ? <Dashboard /> : <Login />}
```


### 8. 條件式為 0 時，通常是非預期的顯示結果
像這樣 `isLoading` 為 boolean，True 顯示 Loading。
```jsx
{isLoading && <Loading />}
```

`''`, `null`, `False` 代表否定、不會顯示任何內容，0 雖然也歸類成否定，對 jsx 卻是可以正確顯示在畫面上的值，務必要讓條件輸出成 boolean 來避免問題。
```jsx
// ✅ 正確
{item.length > 0 && <List item={item} />}

// ❌ 錯誤
{item.length && <List item={item} />} // 得到的結果是 {0}
```


### 10. 列表渲染
JSX 本質上就是 JavaScript + HTML 的混合語法，所有邏輯請交給 JS 處理，Vue.js 的 `v-for` 可以對應到 `.map()`，同樣要設定 `key` 屬性，其值必須唯一。
```jsx
{items.map((item, index) => (
  <li key={index}>{item}</li>
))}
```

