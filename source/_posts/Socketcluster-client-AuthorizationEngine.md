---
title: Socketcluster-client-AuthorizationEngine
tags:
  - SocketCluster
  - Nodejs
date: 2020-09-22 17:45:10
categories:
  - Nodejs
---

# 需求

處理 `JWT` 的時候如果在瀏覽器可以將 token 存在 `localStorage` 中的 `socketcluster.authToken` 

但是如果在 React Native 中沒有 localStorage 的模組

可以使用 [jest-localstorage-mock](https://github.com/clarkbw/jest-localstorage-mock/tree/master/src)

來處理這個問題

但是這樣很醜

希望可以自己控制 Authorization 流程

所以去爬了一下source code

資訊在參考資料

這部分文件沒有寫得很清楚

所以花了一個篇幅來記錄一下如何客製化 Authorization

## Server
# 參考資料

[socketcluster](https://github.com/SocketCluster/socketcluster-client/tree/master/lib)

[authengine](https://github.com/SocketCluster/socketcluster-client/blob/master/lib/clientsocket.js#L138-L142)

[auth.js](https://github.com/SocketCluster/socketcluster-client/blob/master/lib/auth.js)