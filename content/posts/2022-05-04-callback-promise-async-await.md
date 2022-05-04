---
layout: post
title:  "callback promise async/await"
date: 2022-05-04T09:29:06+08:00
draft: false
tags: 
  - "javascript"
---
# 楔子
同樣的 javascript 針對所謂的 async 有其發生的歷史變革，有興趣的可以去找找影片了解一下。

# callback
一開始採用的方式就是使用一個 callback function ，因為 javascript 可以把 function 當參數丟入另一個 function ，就有了最原始的模式，基本的概念就是

```javascript
function executeFunc(callbackFun) {
  // some async function call
  callbackFun()
}
```

- 題型: 請使用 setTimeout 來寫一個 1秒後 console.log(100) 的 callback function case

```javascript
// step 1. define function to run
function go(){
  // step 2. use setTimeout callback function to run after 1 sec
  setTimeout(()=> {
  // step 3. write console 100
  }, sec)
 
}
// step 4. run the function
go()
```

比對一下結果...

```javascript

function delay (callback, sec) {
  setTimeout (callback, sec)
}

// delay 吃一個 callback 的 function
delay(()=> console.log('delay'), 1000)

```

# promise
隨著 es6 Promise 的出現，來改寫一下，這裡蠻重要的，要很習慣把東西包成一包 Promise ，非常常用。

- 題型: 採用 new Promise((resolve, reject) =>{ // do something}) 來把一個 callback function 包裝成 Promise

```javascript
// step 1. pass arguement into Promisified function
function callPromise(val) {
  return new Promise((resolve, reject)=> {
	// step 2. write value as resolve arguement
  })
}

// step 3. run above function and use 'then' method
```

比對一下

```javascript
function callPromise(value) {
  return new Promise((resolve, reject)=>{
    resolve(value)
  })
}

callPromise(100) // get Promise(100)
  .then(x => console.log(x)) // 100
```


# async/await
再來配合寫 code 的習慣，其實在後端程式上，很習慣 code 是一行接著一行，但這和 javascript 的世界有點不太一樣，為何親民化，就多了這個語法糖。

- 題型: 實做一個 callback function 改成 async-await

```javascript
// step 1. Promisify callback function

// step 2. write an async function
async function exeAsyncFun() {
  // step 3. use await inside async
}

// step 4. run above async function
```

```javascript

function callPromise(value) {
  return new Promise((resolve, reject)=>{
    resolve(value)
  })
}

async function exeAsyncFun(value) {
  var v = await callPromise(value)  
	return v
}

exeAsyncFun(100) // 100

```

# 想法
由於 javascript 的歷史變革，配合的 lib 也有其時代背景，在入門時一開始蠻常去試著改寫相關的 function ; 例如看到第三方有一個 callback ，但想實用 async/await 就至少自已可以先包一個 promise 再使用 async/await 。