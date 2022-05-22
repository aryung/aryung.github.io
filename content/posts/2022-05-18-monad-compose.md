---
layout: post
title:  "Monad Compose"
date: 2022-05-18T09:30:06+08:00
draft: false
tags: 
  - "javascript"
  - "functional programming"
---
# 楔子
今天再來進階一下改寫 Monad compose

# Monad

# 特徵
- of:  a => M(a) 也有人叫 lift 或 type lift
- map: map 的 f :: a => M(b) 會變成 M(M(b))
- flatten: M(M(b)) => M(b)

Monad 有 flapMap 的概念
- flatMap = Map + flatten : f(a).flatMap(g) => M(b)

```javascript
const MyMonad = value => ({
  flatMap: f => f(value),
  map (f) {
    return this.flatMap(a => Monad.of(f(a))),
  },
})
Monad.of = x => Monad(x)

Monad(21).map( x => x * 2).map( x => console.log(x))
```

來舉個 Identity Monad 的例子

```javascript
{ // Identity monad
const Id = value => ({
  // Functor mapping
  // Preserve the wrapping for .map() by 
  // passing the mapped value into the type
  // lift:
  map: f => Id.of(f(value)),
  // Monad chaining
  // Discard one level of wrapping
  // by omitting the .of() type lift:
  chain: f => f(value),
  // Just a convenient way to inspect
  // the values:
  toString: () => `Id(${ value })`
})
// The type lift for this monad is just
// a reference to the factory.
Id.of = Id
```

Promise 是一個蠻特別的例子，如果用 promises 來看， `.flapMap` 像什麼? 像不像 `.then`

promise.then(f) 的 f 何時會被呼叫?是當 .then 之後才會執行 f()

拿下面的例子來看，result 也是個 promise (等同於 M(b))

```javascript
{
  const x = 20                 // The value
  const p = Promise.resolve(x) // The context
  const f = n => 
    Promise.resolve(n * 2)     // The function
  const result = p.then(f)     // The application
  result.then(
    r => console.log(r)         // 40
  )
}
```

如果這樣來看，是否可以把 `.then()` 變成 .flatMap()

那 Promise 到底算不算 Monad ? 嚴格來說並不是。

拿個例子來看

```javascript
var p1 = () => Promise.resolve(1) // Promise
var f = x => x + 2 // Monad map?
var p2 = p1().then(f) // Prmoise
```

f 應該是 a => M(b) 的概念，但上面的 f 是 a => b 的概念，還是有些許的差異，不過就看我們怎去處理，在嚴格的一些數學轉換上，Promise 可能就會有些例外狀況。

# Kleisli Composition Function
建一個 promise-lifting 的 compose function

```javascript
const composeM = method => (...ms) => (
  ms.reduce((f, g) => x => g(x)[method](f))
)
```

標準的 compose map

```javascript
{
  // The algebraic definition of function composition:
  // (f ∘ g)(x) = f(g(x))
  const compose = (f, g) => x => f(g(x))
  const x = 20    // The value
  const arr = [x] // The container
  // Some functions to compose
  const g = n => n + 1
  const f = n => n * 2
  // Proof that .map() accomplishes function composition.
  // Chaining calls to map is function composition.
  trace('map composes')([
    arr.map(g).map(f),
    arr.map(compose(f, g))
  ])
  // => [42], [42]
}
```

改寫一下

```javascript
const composeMap = (...ms) => (
  ms.reduce((f, g) => x => g(x).map(f))
)
```

複製一下上面的東西來修改，其實這個很符合 api 的觀念，打 api 之後(async)取得一包值(a)，再用這包值(a)去打別的 api aysnc 取得 b，大概的觀念就像 a => M(b)，b => M(c)，這個比 a => b => c 複雜多了一層。

```javascript
{
  const composePromises = (...ms) => (
    ms.reduce((f, g) => x => g(x).then(f))
  );
  const label = 'Promise composition'
  const g = n => Promise.resolve(n + 1)
  const f = n => Promise.resolve(n * 2)
  const h = composePromises(f, g)
  h(20)
    .then(trace(label))
  // Promise composition: 42
}
```

再來過一層包裝

```javascript
const composeM = method => (...ms) => (
  ms.reduce((f,g) => x => g(x)[method](f))
)
```

重新定義一下

```javascript
const composePromise = composeM('then')
const composeMap = composeM('map')
const composeFlatMap = composeM('flatMap')
```


資料來源
- [Eric Eliott Compose software](https://medium.com/javascript-scene/javascript-monads-made-simple-7856be57bfe8)
