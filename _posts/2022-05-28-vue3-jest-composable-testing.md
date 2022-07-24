---
title: Vue3+jest 測試 composable 範例
date:   2022-05-28 08:00:00
categories: [Unit Test, Jest, Vue.js, Vue]
tags: [Node.js, Javascript, Vue.js, Vue3, Composition API, Unit Test, Jest, Composable]
---
## Composable 與 Mixin 比較
Vue3 的 `composable` 乍看下和 `mixin` 用途很類似，可以提供各個元件共用程式碼。但與 mixin 相比，composable 主要有三個優勢：

第一，元件可以很明確的區分使用的 composable 來源，當使用的 mixin 一多時，追朔來源相對困難，無法一眼看出由哪個 mixin 實作。

第二，多個 mixin 無法確保使用了相同的名稱，可能造成覆蓋，但 composable 即使有相同的名稱，也能透過結構式賦值、重新命名。

最後，多個 mixin 需要交互作用時，通常會使用相同的參數命名來達到這個目的，隱性的耦合使得辨識和debug難度增加，composable可藉由其一的回傳值，作為其他composable輸入的參數達到共享的目的。([參考資料](https://vuejs.org/guide/reusability/composables.html#vs-mixins))


## 開始測試

安裝 Jest 測試 vue 時，首先要注意版本，請參考下面的對照表安裝套件：

| Vue version    | Jest Version      | npm Package |
|:--------------|:-----------------|-----------:|
| Vue 2          | Jest 26 and below | vue-jest@4  |	
| Vue 3	| Jest 26 and below | vue-jest@5 |
| Vue 2	| Jest 27 and above	| @vue/vue2-jest@27 |
| Vue 3	| Jest 27 and above	| @vue/vue3-jest@27 |
| Vue 2	| Jest 28 and above	| @vue/vue2-jest@28 |
| Vue 3 | Jest 28 and above	| @vue/vue3-jest@28 |


### With lifecyle
composable 的測試主要分成兩種，第一種元件比較無關，可以當作普通的 js code 測試。
```js
import {ref} from 'vue';

export function useCounter() {
  const count = ref(0);
  const increment = () => count.value++;

  return {
    count,
    increment,
  };
};
```
{: file='counter.js'}

```js
import {useCounter} from './counter.js';

test('useCounter', () => {
  const {count, increment} = useCounter()
  expect(count.value).toBe(0);

  increment();
  expect(count.value).toBe(1);
});
```
{: file='counter.test.js'}


### With lifecyle
如果 composable 牽涉到元件 lifecyle hooks 或是 provide/inject 時，需要依附一個元件進行測試。
```js
import {createApp} from 'vue';

export function withSetup(composable) {
  let result;

  const app = createApp({
    setup() {
      result = composable();
      // suppress missing template warning
      return () => {};
    },
  });

  app.mount(document.createElement('div'));
  // return the result and the app instance
  // for testing provide / unmount
  return [result, app];
};
```
{: file='test-utils.js'}

```js
import {ref, onMounted} from 'vue';

export function useCounter() {
  const count = ref(0);
  const increment = () => count.value++;
  
  onMounted(() => {
    increment();
  });

  return {
    count,
    increment,
  };
};
```
{: file='counter.js'}

`result` 是 composable 的回傳值(return)，測試裡面 `app.mount();`，將執行 `onMounted`：
```js
import {withSetup} from './test-utils';
import {useCounter} from './counter.js'

test('useCounter', () => {
  const [result, app] = withSetup(() => useCounter());

  // run assertions
  expect(result.count.value).toBe(0);

  // trigger onMounted hook if needed
  app.mount();

  expect(result.count.value).toBe(1);
});
```
{: file='counter.test.js'}

<br>
另外還有一個常犯的錯誤，先看 composable 程式碼，有一個 computed 使用 formatFn 轉換 list：
```js
import {computed, unref} from 'vue';

export function useListFormatter(list, formatFn) {
  const formattedList = computed(() => (
    unref(list).map(item => formatFn(item))
  ));

  return {
    formattedList,
  };
};
```
{: file='useListFormatter.js'}

測試時需要注意回傳值被使用之前，`formattedList` 不會被執行。
```js
import {useListFormatter} from './useListFormatter.js';

test('useListFormatter', () => {
  const list = ['a', 'b'];
  const formatFn = jest.fn();

  formatFn.mockImplementation(value => `new-${value}`)

  const {formattedList} = useListFormatter();
  
  // Don't do it! formatFn won't be excuted until formattedList is called.
  // expect(formatFn).toHaveBeenCalled();
  
  expect(formatFn).not.toHaveBeenCalled();

  expect(formattedList.value).toEqual(['new-a', 'new-b']);
  expect(formatFn).toHaveBeenCalledTimes(2);
  expect(formatFn).toHaveBeenNthCalledWith(1, 'a');
  expect(formatFn).toHaveBeenNthCalledWith(2, 'b');
});
```
{: file='useListFormatter.test.js'}

參考資料： [https://vuejs.org/guide/scaling-up/testing.html#testing-composables](https://vuejs.org/guide/scaling-up/testing.html#testing-composables)
