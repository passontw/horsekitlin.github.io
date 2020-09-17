---
title: Redux-Part-I
tags:
  - React
  - Redux 
date: 2020-09-16 01:49:37
categories:
  - React
---

# Redux

Flux 的實作之一

有一個共同的 Store 儲存資料

透過 `Middleware` 來控制工作流

發起 `Action` 來通過工作流之後

透過 `Reducer` 來修改 Store 內的資料

再藉由 Store 的資料來顯示 Screen

搭配的 Package 有很多種不同的配合與實現

* redux-thunk
* redux-saga

主要都是在處理 [Async Actions](https://redux.js.org/advanced/async-actions)

[redux-thunk skelton](https://github.com/coodoo/react-redux-isomorphic-example)

[redux-saga skelton](https://github.com/horsekitlin/react-skelton/tree/develop)

兩個骨架範例

基本上每一個 `Async Action` 都是單一的 `Promise`

但是不代表只能夠有一個 `Promise`

也可以多個 `Promise` 但是需要使用 `Promise.all` 做群組

有清楚的成功與失敗

所以建立一個非同步的 `Action` 應該會有三個 `action types`

* Request - 顯示 loading
* Success - 成功 (關閉 loading)
* Error - 失敗 (關閉 loading)

不要跳脫一個完整的週期

![image](https://blog.krawaller.se/static/posts/react-js-architecture-flux-vs-reflux/img/flux-diagram.png)

在一個 `Action` 內 在發送另外一個 **非同步** Action
