---
layout: post
title:  "react rule of hook"
date: 2022-05-10T09:30:06+08:00
draft: false
tags: 
  - "javascript"
	- "react"
	- "hook"
---

# 楔子
`hook` 應該是 react 改成 functional component 最方便用的方式，在整個 code syntax 也是簡潔，不過當新舊交換時，常常難免會有一些使用上的規範。

# rule of hook

直接用 Tyler 的範例來標示 rule

```
function Counter () {
  // 👍 from the top level function component
  const [count, setCount] = React.useState(0)

  if (count % 2 === 0) {
    // 👎 not from the top level
    React.useEffect(() => {})
  }

  const handleIncrement = () => {
    setCount((c) => c + 1)

    // 👎 not from the top level
    React.useEffect(() => {})
  }

  ...
}

function useAuthed() {
  // 👍 from the top level of a custom Hook
  const [authed, setAuthed] = React.useState(false);
}

class Counter extends React.Component {
  render() {
    // 👎 from inside a Class component
    const [count, setCount] = React.useState(0);
  }
}

function getUser() {
  // 👎 from inside a normal function
  const [user, setUser] = React.useState(null);
}
```
The reason for this rule is because React relies on the call order of Hooks to keep track of internal state and references. If your Hooks aren't called consistently in the same order across renders, React can't do that.

參考資料
- [React rule of hook](https://zh-hant.reactjs.org/docs/hooks-rules.html)
- [Tyler hook of rule](https://ui.dev/react-hooks)