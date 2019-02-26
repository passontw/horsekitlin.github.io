---
title: ReasonReact-RenderAndCallbackHandler
tags:
---

# Render

`render` 是一個 function

需要回傳的是一個 `ReasonReact.reactElement`

* <div />
* <MyComponent />

之類的

會有一個參數 `self`

在 ReactJs 中你可以回傳任意的值

`null`, `<div> hello </div>`, `<div> {1} </div>` 都可以輕易達成

但是在 Reason 中 type system 會限制你在 `render` 只能夠回傳 `ReasonReact.reactElement`

* `ReasonReact.null` 這個就等同 React 中的 null
* `ReasonReact.string` 會將字串轉為 `reactElement`
* `ReasonReact.array` 會將陣列轉為 `reactElement`

# Callback Handler

這一部分要解釋的是在 Reason 中如何綁定 Event

範例 `<div onClick={this.handleClick} />`

## Callback Without State Update

這個會有兩種狀況

### Not Reading Into self

**Note: self 是 Reason 的 this, 是 recode 模式像是 state**

如果只是傳遞給子 Component

那做法會跟 React 一樣

```reason
let component = ReasonReact.statelessComponent("Button");

let make = (~onClick, _children) => {
  ...component,
  render: (_self) => <button onClick=onClick />
};
```

### JSX

Reason JSX 沒有和 ReactJS 綁定

#### 大寫字首標籤

```jsx
<MyComponent foo={bar} />
```

變成

```reason
([@JSX] MyComponent.createElement(~foo=bar, ~children=[], ()));
```

#### 非大寫字首標籤

```jsx
<div foo={bar}> child1 child2 </div>;
```

變成

```reason
([@JSX] div(~foo=bar, ~children=[child1, child2], ()));
```

#### Fragment

```jsx
<> child1 child2 </>;
```

變成

```reason
([@JSX] [child1, child2]);
```

#### children

```jsx
<MyComponent> foo bar </MyComponent>
```

變成

```reason
([@JSX] MyComponent.createElement(~children=[foo, bar], ()));
```

#### 雙關(punning)

```reason
let component = ReasonReact.statelessComponent("Button");

let make = (~onClick, _children) => {
  ...component,
  render: (_self) => <button onClick=onClick />
};
```

也可以簡化為

```reason
let component = ReasonReact.statelessComponent("Button");

let make = (~onClick, _children) => {
  ...component,
  render: (_self) => <button onClick />
};
```

### Reading Into self

要取得 `state`, `send` 要透過一個 callback 去呼叫 `self`

```reason
let component = ReasonReact.statelessComponent("Page");;

let make = (~name, ~onClick, _children) => {
  let click = (event, self) => {
    onClick(event);
    Js.log(self.state);
  };
  {
    ...component,
    initialState: /* ... */,
    render: (self) => <button onClick={self.handle(click)} />
  }
};
```

事實上， self.handle 只是一個普通的函數

它接受兩個參數

第一個是 callback function 本身

第二個參數是要傳給 callback function 的數據（payload）

由於Reason語言 天生的柯里化

我們通常只要你傳遞第一個參數

然後返回一個新的函數

新的函數會接受第二個函數 然後執行函數本體

第二個參數（callback 中的 self）是由函數caller傳進來的

就是你正在 rendering 的 Component 本身

有時候你會需要放入多個參數

在 React 中就像是

```javascript
handleSubmit: function(username, password, event) {
  this.setState(...)
}
...
<MyForm onUserClickedSubmit={this.handleSubmit} />
```

但是在 Reason 中不能這樣寫

因為 handle 只能丟入一個參數

所以處理這種狀況會是下面的解法

```reason
let handleSubmitEscapeHatch = (username, password, event) =>
  self.handle(
    (tupleOfThreeItems, self) => doSomething(tupleOfThreeItems, self),
    (username, password, event),
  );
...
<MyForm onUserClickedSubmit=(handleSubmitEscapeHatch) />
```

總括來說 callback 需要
* 接受 JS component function 傳來的多個參數
* 把接受的參數包裝成一個 Tuple 然後使用 self.handle
* 建立另一個函數 (接受一個 Tuple) 傳給 handle
* 呼叫一次 self.handle (第一個參數是接受一個 Tuple 函數, 第二個參數是傳入的 Tuple)