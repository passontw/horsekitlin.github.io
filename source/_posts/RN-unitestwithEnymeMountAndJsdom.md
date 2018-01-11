---
title: RN-使用Enzyme 的 mount 測試 React Native
tags:
  - Javascript
  - React Native
date: 2018-01-11 10:05:03
categories:
---

# React 與 React Nativee

如果使用同一個架構的話 `React` 與 `React Native` 是大同小異的

但是基於兩個的底層是完全不同的

一個是 `Web HTML` 一個是 `Native code`

希望使用盡量一致的 Lib 來做測試似乎困難度有點高

雖然尚未有完全無違和的測試

還是可以利用 `Jest` + `Enzyme` + `Jsdom` 來為 React Native 模擬 mount 環境

續前章 [初步使用 Jest + Enzyme 做 React Native 測試](./RN-JestErrorWithRNRouterFlux.md)

如果要在 測試中使用 `mount` 的話

會顯示 `document is undefined` 的錯誤

所以為了彌補這個問題

我們需要做一些補充

# shallow and mount 的不同

## shallow

`shallow` 針對 Component 做單一的單元測試，並不會直接顯示他的 `Children Component`

## mount

`mount` 會完整的 render 所有的 `Component` 包含他下層的所有 `Component`

# Installing

## Step1

```
  $ npm install --save-dev react-native-render-mock jsdom --save-dev
```

## add SetupFile.js

```javascript
const { JSDOM } = require('jsdom');

const jsdom = new JSDOM();
const { window } = jsdom;

function copyProps(src, target) {
  const props = Object.getOwnPropertyNames(src)
    .filter(prop => typeof target[prop] === 'undefined')
    .map(prop => Object.getOwnPropertyDescriptor(src, prop));
  Object.defineProperties(target, props);
}

global.window = window;
global.document = window.document;
global.navigator = {
  userAgent: 'node.js',
};
copyProps(window, global);

import Enzyme from "enzyme";
import Adapter from "enzyme-adapter-react-16";

Enzyme.configure({ adapter: new Adapter() });

// Ignore React Web errors when using React Native
console.error = (message) => {
  return message;
};

require('react-native-mock-render/mock');
```

在這邊會幫助你 mock 一個 `global.document`

但是執行的時候可能會出現 `jsdom` 的  class XMLHttpRequest 缺少 `super` 的問題

```
  $ vim ./node_modules/jsdom/lib/jsdom/living/xmlhttprequest.js
```

在 `XMLHttpRequest` 的 `constructor` 中加上 `super()`

然後執行 `npm test`

就可以開始執行你的 React Native unitest  之路了