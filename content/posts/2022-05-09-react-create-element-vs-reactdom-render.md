---
layout: post
title:  "callback promise async/await"
date: 2022-05-09T09:29:06+08:00
draft: false
tags: 
  - "javascript"
  - "react"
---

# 楔子
最近小夥伴問了，React 的 jsx 基本的結構長如何，是如何轉換的之類的，順便記錄一下網路大神的資料摘錄。
- React 有一個 createElement 的方法可以把資料轉成 react 特殊的結構 `React.createElement('div', props={})`
- 收到特殊的結構之後再由 ReactDOM.render 來進行放在 html 上的位置 `ReactDOM.render(jsDOM, document.getElementById('root'))`

# React Basic
# React vs ReactDOM 

摘錄一些 Kent 的示範 code 來演示一下，element 1 ~ 3 就是各種 jsDOM 的寫法，比較特別的是 React.createElement 可以有 2 ~3 個輸入參數
- 第一個參數指的是 HTML 的 DOM 種類(eg. div span ...)
- 第二個參數輸入的是該 DOM 的 props
- 第三個參數輸入的是 children

```javascript
const element1 = React.createElement('div', {className: 'container'})
const element2 = React.createElement('div', {className: 'container'}, 'Hello', '', 'World')
const element3 = React.createElement('div', children: ['Hello', '', 'World'])
ReactDOM.render(element, document.getElementById('root'))
```

再試試看不同的輸入方式

```javascript
const helloElement = React.createElement('span', null, 'Hello')
const worldElement = React.createElement('span', {children: 'World'})

const element4 = React.createElement('div', children: [helloElement, '', worldElement]) // key pro

const element5 = React.createElement('div', {className: 'container'}, helloElement, '', worldElement ) 

ReactDOM.render(element4, document.getElementById('root'))
ReactDOM.render(element5, document.getElementById('root'))
```

## JSX

jsx 的格式大概長相就是 `<div {...props}>{children}<div>`

```jsx
const className = 'container'
const children = 'Hello World'
const element = <div className={className}>{children}</div>
```

再做一些 spread 展開的運用方式

```jsx
const className = 'container'
const children = 'Hello World'
const props = {children, className}

const element = <div className={props.className}>{props.children}</div>

const element2 = React.createElement('div', {id:'my-thing', ...props})

ReactDOM.render(element, document.getElementById('root'))
ReactDOM.render(element2, document.getElementById('root'))
```