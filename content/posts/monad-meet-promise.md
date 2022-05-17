---
layout: post
title:  "Monad vs Promise"
date: 2022-05-17T09:30:06+08:00
draft: false
tags: 
  - "javascript"
  - "functional programming"
---
# 楔子
來試著說明何謂 monad 這其實是數學的一個名稱，但撇開那些艱深的道理，試試來用 Promise 的例子來說明看看。

# Function Compose

最原始的 compose 的觀念就是把多個 function 做組合

```javascript
const x = 20
const f = n => n * 2
const arr = Array.of(x)

const result = arr.map(f)
```

例如 `echo` 就是吃二個參數回傳一個 function

```javascript
const echo = n => x => Array.from({length: n}).fill(x)

console.log(
  [1,2,3].map( echo(3) )
)
```

例如 `flatMap` 就是吃二個參數回傳一個 function

```javascript
const flatMap = (f, arr) => [].concat(...arr.map(f))
const echo = n => x => Array.from({length: n}).fill(x)
console.log(
  flatMap( echo(3), [1,2,3])
)
```

公式鏈
- g: a => b
- f:       b => c
- h: a      =>  c

典型的 compose 例子，看圖就可以了解


![function compose](https://upload.cc/i1/2022/05/17/EUmteT.png)

公式鏈
- g: F(a) => F(b)
- f:          F(b) => F(c)
- h: F(a)          => F(c)

這是開始進入 functional programming 的範圍，把 F(a) 視為一個容器，包著一個值 a，而這個容器也可以用 compose function 的概念。

![monad compose](https://upload.cc/i1/2022/05/16/CHrw7T.png)

公式鏈
- f: a => M(b)
- g:      ???     b => M(c)
- h: a.             =>  M(c)

如果再使用成另一個 container (Monad)
![functor map](https://upload.cc/i1/2022/05/16/7Dr3QC.png)

拿一個實在的例子 `Promise` 常常會在 console 下看到 `Promise<pending>`

```javascript
getUserById(id: String) => Promise(User)
hasPermision(User) => Promise(Boolean)
```

這是一個純粹用 value 的 compose 的例子，純粹使用 function 的 compose

```javascript
const compose = (...fns) => x => fns.reduceRight((y, f) => f(y))

const trace = label => value => {
  console.log(`${label}: ${value}`)
}
```

這是使用一個 Promise 的 map 但如果純粹使用會有噴 error

```javascript
const label = 'API call composition'
// a => Promise(b)
const getUserById = id => id === 3 ? Promise.resolve({name: 'Kurt', role: 'Author'}) : undefined

// b => Promise(c)
const hasPermission = ({role}) => (
  Promise.resolve(role === 'Author')
)

// Try to compose them. Warnning: this will fail
const authUser = compose(hasPermission, getUserById)

// Oops! Always false!
authUser(3).then(trace(label))
```

需要再進行一次 `.then()` 的轉化

```javascript
const composeM = flatMap => (...ms) => (
  ms.reduce((f, g) => x => g(x)[flatMap](f))
)

const composePromises = composeM('then')

const label = 'API call composition'

// a => Promise(b)
const getUserById = id => id === 3 ? Promise.resolve({name: 'Kurt', role: 'Author'}) : undefined

// b => Promise(c)
const hasPermission = ({role}) => (
  Promise.resolve(role === 'Author')
)

// Try to compose them. Warnning: this works!
const authUser = composePromises(hasPermission, getUserById) 

authUser(3).then(trace(label)) // true

```

資料來源
- [Eric Eliott Compose software](https://medium.com/javascript-scene/javascript-monads-made-simple-7856be57bfe8)
