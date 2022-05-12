---
layout: post
title:  "升級 javascript 到 typescript"
date: 2022-05-12T09:30:06+08:00
draft: false
tags: 
  - "typescript"
	- "functional programming"
---

# 楔子
typescript 針對 javascript 有進行型別的補強，在 functional programming 的 lib 中，有 `fp-ts` 可以使用，不過個人覺的它有點複雜變的不太好用，它的型別有點太多了，先了解它的東西應該就飽了，那今天來試試改用小刀水平的 javascript 吧。

# 說明
先要熟悉一下 arrow function 的 typescript 有點不易閱讀(官網也這樣說)

這是一個取第一個值的簡單函數

```typescript
let fst: (a: any, b: any) => any = (a, b) => a
```

如果套上括號(弄個特殊的括號 [ ] )，應該就可以比較好懂這個格式了。

`let fst: [(a: any, b: any) => any] = (a, b) => a`

比較容易理解的還是用純 function 來寫

```typescript
function fst(a: any, b: any): any {
  return a
}
```

如果再套上 Generic types 的表達格式

```typescript
function fst<T,U>(a: T, b: U): T {
  return a
}
```

接下來開始改造 lib 升級到 typescript 先從小的東西開始，如果原來的 javascript 

```javascript
export function Box(x){
  return {
    x,
    map: (f) => Box(f(x)),
    fold: (f, g) => f(x),
    chain: (f) => f(x),
    inspect: () => `Box(${x})`
  }
}
```

typescript 的概念就是幫 javascript + type (好像廢話)，所以讓自已記住一個很重要的起手示，就是要寫 type & function 要定義缺失的 type (eg. 參數...)

```typescript
// step 1. defined types
type BoxProps<T> = {
  x: T
  map(f: Function): BoxProps<T>
  fold(f: Function, g: Function): T
  chain(f: Function): T
  inspect: () => String
}

// step 2. write function & fill missing types
export function Box<T>(x: T): BoxProps<T> {
  return {
    x,
    map: (f) => Box(f(x)),
    fold: (f, g) => f(x),
    chain: (f) => f(x),
    inspect: () => `Box(${x})`
  }
}
```

補上完整的作業 code

```typescript
type BoxProps<T> = {
  x: T
  map(f: Function): BoxProps<T>
  fold(f: Function, g: Function): T
  chain(f: Function): T
  inspect: () => String
}

export function Box<T>(x: T): BoxProps<T> {
  return {
    x,
    map: (f) => Box(f(x)),
    fold: (f, g) => f(x),
    chain: (f) => f(x),
    inspect: () => `Box(${x})`
  }
}

type RightProps<T> = {
  x: T
  map(f: Function): RightProps<T>
  fold(f: Function, g: Function): T
  chain(f: Function): T
  inspect: () => String
  // ap(y: RightProp<T>): RightProp<T>,
}

export function Right<T>(x: T): RightProps<T> {
  return {
    x,
    chain: (f) => f(x),
    map: (f) => Right(f(x)),
    fold: (f, g) => f(x),
    inspect: () => `Right(${x})`
    // ap: other => other.map(x),
    // traverse: (of, f) => f(x).map(Right),
  }
}

type LeftProps<T> = {
  x: T
  map(f: Function): LeftProps<T>
  fold(f: Function, g: Function): T
  chain(f: Function): LeftProps<T>
  ap(f: Function): LeftProps<T>
  inspect: () => String
}

export function Left<T>(x: T): LeftProps<T> {
  return {
    x,
    chain: (f) => Left(x),
    ap: (other) => Left(x),
    map: (f) => Left(x),
    fold: (f, g) => f(x),
    inspect: () => `Left(${x})`
    // traverse: (of, f) => of(Left(x)),
  }
}

export function fromNullable<T>(x: T): RightProps<T> | LeftProps<null> {
  return x === null || x === undefined ? Left(null) : Right(x)
}

export function tryCatch(f: Function) {
  try {
    return Right(f())
  } catch (e) {
    return Left(e)
  }
}

let a = fromNullable(null)
console.log(a.inspect())
let b = () => {
  throw new Error('yoyo')
}

let c = tryCatch(b)

console.log(c.inspect())

let d = fromNullable(3).map((x) => x + 6)
console.log(d.inspect())

let e = Left(3).map((x) => x + 1)
console.log(e.inspect())
```

參考資料
- [demo code](https://codesandbox.io/s/agitated-dubinsky-e9xyju?file=/src/index.ts)