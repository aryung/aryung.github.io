---
layout: post
title:  "React 設計模式: Compound Components"
date: 2022-03-10T18:29:06+08:00
draft: false
tags: 
  - "react"
---

# 楔子
摘錄 Kent 的 [React Advance Pattern: Compond Components](https://github.com/kentcdodds/advanced-react-patterns/blob/main/src/final/02.js)

這個設計模式有什特殊的點?

如果要做一個切換的鈕的元件，大概就會用一個變數來判斷要如何切換?

```
function App() {
  const [on, setOn] = React.useState(false)
  return (
    <div>
      { on ?  <div>The button is on</div> : null }
      { on ?  null : <div>The button is off</div> }
    </div>
  )
}
```

這樣的 code 就有點醜，那有沒有可能性把 code 長成這樣 ?

```
function App() {
  return (
    <div>
      <Toggle>
        <ToggleOn>The button is on</ToggleOn>
        <ToggleOff>The button is off</ToggleOff>
        <ToggleButton />
      </Toggle>
    </div>
  )
}
```

來完成 `Toogle` `ToogleOn` `ToogleOff` 這三個元件的寫法呢?

這樣的 component 看來就比較有水準了吧

```
// Switch 就是一個元件用屬性判斷長相
function ToggleOn({on, children}) {
  return on ? children : null
}

function ToggleOff({on, children}) {
  return on ? null : children
}

function ToggleButton({on, toggle, ...props}) {
  return <Switch on={on} onClick={toggle} {...props} />
}
```

想一下怎包裝 ?

```
function Toggle({children}) {
  const [on, setOn] = React.useState(false)
  const toggle = () => setOn(!on)
  // return ?????
}
```

這時可以參考 `React.Children` 的用法 [React Children](https://reactjs.org/docs/react-api.html#reactchildren) 和 [React Clone](https://reactjs.org/docs/react-api.html#cloneelement)
    
```
function Toggle({ children }) {
  const [on, setOn] = React.useState(false);
  const toggle = () => setOn(!on);

  return React.Children.map(children, (child) =>
    React.cloneElement(child, { on, toggle })
  );
}
```

但如果 `Toogle` 的元件中包了一些沒有 {on, toogle} 屬性的元件時，會噴錯哦(例如在下面的 `span`)

```
function App() {
  return (
    <div>
      <Toggle>
        <ToggleOn>The button is on</ToggleOn>
        <ToggleOff>The button is off</ToggleOff>
        <span>Hello</span>
        <ToggleButton />
      </Toggle>
    </div>
  )
}
```

這時就要修正一下 `React.Children`

```
function Toggle({children}) {
  const [on, setOn] = React.useState(false)
  const toggle = () => setOn(!on)
  return React.Children.map(children, child => {
    return typeof child.type === 'string'
      ? child
      : React.cloneElement(child, {on, toggle})
  })
}
```

如果要限制某些元件

```
const Compos = [Toogle, ToogleOn, ToogleOf]
function Toggle({children}) {
  const [on, setOn] = React.useState(false)
  const toggle = () => setOn(!on)
  return React.Children.map(children, child => {
    return Compos.includes(child) 
      ? React.cloneElement(child, {on, toggle})
      : null
  })
}
```

比較詳細的有興趣可以去看一下 [Kent Github](https://github.com/kentcdodds/advanced-react-patterns/blob/main/src/final/02.js) 或 [epicReact](https://epicreact.dev)