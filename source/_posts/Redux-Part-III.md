---
title: Redux-Part-III
tags:
  - React
  - Redux 
date: 2020-09-18 00:49:44
categories:
  - React
---

# 前情提要

角色

* 生產者 - 產出任務 `store.dispatch`
* 消費者 - 消費任務 `saga function`
* channel - 暫存任務的地方

# Redux-Saga 的組成

* createMiddleware
* effects
* Channel

## createMiddleware

基於 redux 所以要建立一個 `sagaMiddleware`

[createMiddleware](https://github.com/redux-saga/redux-saga/blob/master/packages/core/src/internal/middleware.js)

會回傳一個 `sagaMiddleware`

### sagaMiddleware

在 `sagaMiddleware` 有一個 `run` 的參數

他是之前說過的 Generator Runner 

[sagaRunner](https://github.com/redux-saga/redux-saga/blob/master/packages/core/src/internal/runSaga.js)

第一個參數是傳入一個物件

在這個 `Function` 中 會利用 `saga` 產生 iterator



#### Channel

之前在生產者產生 task 之後需要有一個 channel 來暫存

這就是暫存的地方

預設會有一個 channel(稍等再說)

可以透過參數傳送一個 channel 

否則會自動產生一個

#### dispatch, getState

store 的參數傳入

#### context

待補

#### sagaMonitor

[Interfaces](https://redux-saga.js.org/docs/api/)

提供外部的 Monitor 接口

#### effectMiddlewares

待補

#### onError

預設 logError

待補

# 參考資料

[createMiddleware](https://github.com/redux-saga/redux-saga/blob/master/packages/core/src/internal/middleware.js)