---
title: Redux-Part-III
tags:
  - React
  - Redux 
date: 2020-09-18 00:49:44
categories:
  - React
---

# 實做一個簡單的 Redux-Saga

Redux-Saga 是基於 Producer/Consumer Pattern 實作程式碼

生產與消費的對象稱作 task 或 taker

**Saga function 是我們在使用 redux-saga 時，會用 generator 撰寫處理非同步的函式**

👉 當生產一個 task 時，會先把一個 saga function 轉換成 iterator

```javascript
function* saga() {
  yield console.log('1');
  yield console.log('2');
  yield console.log('3');
}

const iterator = saga();
```

然後用 `Generator runner` 把 iterator 包裝成一個可以自動迭代的函式 next()

而這個 next() 就是 task (taker)

**next()**

```javascript
function runner(itr) {
  function next(arg) {
    let result = itr.next(arg);
    if (result.done) {
      return arg;
    } else {
      return Promise.resolve(result.value).then(next);
    }
  }
  return next();
}

// 將上方的 iterator 傳入
runner(iterator)
```

當需要消化一個 `task` 的時候 會呼叫 `next()` 自動迭代 iterator

## 和 redux-saga 溝通

前面的都是 `Saga` 內部的實做 尚未與外部溝通

所以需要讓外部有可以呼叫到  redux-saga 中的 producer 與 consumer

這時候就需要 **saga middleware**

它需要有兩個功能

一個是外部呼叫後 可以在 channel 中新增一個 `task`

另一個是在外部呼叫 `store.dispatch(action)` 後，消耗在 channel 中相對應的 task

## 5 種 saga effect

* take
* call
* put
* takeEvery
* fork 

例如經常使用的 `call`:

* type ：執行的 effect 型別 
* fn：被執行的函式
* args ：被執行的函式需要帶入的參數

```javascript
export function call(fn, ...args) {
  return {
    isEffect: true,
    type: "call",
    fn,
    args
  };
}
```

## Effect Runner

每一種 effect 都有其相對應的 effect runner

讓每個 effect runner 各司其職

不用把很多概念混雜在一起

分開時會比較好管理

舉例來說，call 的 effect runner 會長這個樣子

* fn : 在 saga function 中我們定義的 callback function
* args : 在 saga function 中我們指定的 fn — callback function 傳入的參數
* next : Generator runner 中的 next()，用於進行下一次迭代

```javascript
function runCallEffect({ fn, args }, next) {
  fn.call(null, args)
    .then(success => next(null, success))
    .catch(error => next(error));
}
```

[function call](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call)

