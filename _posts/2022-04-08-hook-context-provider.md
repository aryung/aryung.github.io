---
layout: post
title:  "useContext Provider 起手式"
published: true
tags: 
  - ""
---
# 楔子
useContext 是 hook 系列很常使用的東西，通常就在「跨」 components 之間使用

有時加減也好用也有點濫用，說說比較常使用的情境吧..

例如像 Global Theme 在所有的 component 都包上一層，像 Material UI 就採用這種模式..

那像假設 firebase Oauth 的情況時，要用嗎?還是可以用 custom hook ?

其實這二種方式都有人用，看自已的習慣而以..

# 一般式
這是比較正常的方式，最外層包一個 Provider 再利用 render prop 來操作

{% highlight javascript %}
import React from 'react';

export const UserContext = React.createContext();

export default function App() {
  return (
    <UserContext.Provider value="user">
      <User />
    </UserContext.Provider>
  )
}

function User() {
  return (
    <UserContext.Consumer>
      {value => <h1>{value}</h1>} 
    </UserContext.Consumer>
  )
}
{% endhighlight %}

# Kent c dodds
但其實如果使用 Kent 的起手式，其實蠻優美的..
[Kent useContext code](https://github.com/kentcdodds/advanced-react-hooks/blob/main/src/final/03.extra-1.js)

用一個 component 當 Provider 同時也可以在那個 Provider 內使用 useEffect 之類的功用..

{% highlight javascript %}
import * as React from 'react'

const CountContext = React.createContext()

function CountProvider(props) {
  // 在這可以塞更多 hook 的變數或 function
  const [count, setCount] = React.useState(0)
  const value = [count, setCount]
  return <CountContext.Provider value={value} {...props} />
}

function useCount() {
  const context = React.useContext(CountContext)
  if (!context) {
    throw new Error('useCount must be used within a CountProvider')
  }
  return context
}

function CountDisplay() {
  const [count] = useCount()
  return <div>{`The current count is ${count}`}</div>
}

function Counter() {
  const [, setCount] = useCount()
  const increment = () => setCount(c => c + 1)
  return <button onClick={increment}>Increment count</button>
}

function App() {
  return (
    <div>
      <CountProvider>
        <CountDisplay />
        <Counter />
      </CountProvider>
    </div>
  )
}

export default App
{% endhighlight %}