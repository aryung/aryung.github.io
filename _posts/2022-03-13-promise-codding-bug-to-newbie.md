---
layout: post
title:  "Promise 新手常花時間的坑"
published: true
tags: 
  - "react"
---

# 楔子
最近有些小夥伴在彼此討論時，常常出現一些不太知道原因，但改一改就好了，就隨手做一下筆記也方便提醒自已。

# 狀況一: 分不清楚有沒有回傳值

{% highlight javascript %}
functo go1 () {
  return 1
} // 有回傳值

functin go2 () {
  console.log(3)
} // 無回傳值

const go3 = () => 2

const go4 = () => {
  console.log(4)
} // 無回傳值

function addOne (x) {
  return x
}
const go5 = () =>
  addOne(3)

const go6 = () => {
  7
}
const go7 = (x) => ({x})
{% endhighlight %}

從上面的問題來看，最直覺的就是要有 `return` 字眼就是代表有回傳值，所以 go1 go2 很容易判斷。

在 arrow function 中， function 可以直接寫 `const go3 = () => 2` 或 `const go6 = () => {7}` 來簡化，但前者有返回值而後者並沒有返回值。

但偏偏又有一個例子 `const go7 = (x) => ({x})` 這代表直接返回一個 object `({x})`

# 易發生的 use case
上面的例子最常發生在 `.then` 的情況下

{% highlight javascript %}
function getAddOne(x) {
  return new Promise((resolve, reject) => {
    resolve(x + 1)
  })
}

const getAddTwo = (x) => Promise.resolve(x+2)

function getTimesTen(x) {
  return new Promise((resolve, reject) => {
    resolve(x * 10)
  })
}

const getTimesTwenty = (x) => Promise.resolve(2 * x)

function errGetAddOne(x) {
  new Promise((resolve, reject) => {
    resolve(x + 1)
  })
}

getAddOne(10).then(x => getTimesTen(x)) //?
errGetAddOne(10).then(x => getTimesTen(x)) //?

{% endhighlight %}

可以試一下上面的 demo code，就會發現所謂的 

`.then(// do something func).then()`

這樣的東西能夠串下去，最重要的是上面要回傳一個 `Promise` 它才有 `.then` 的方法

所以在寫 function 時都要想一下這個 function 是只是執行東西不用回拋(eg. 很多主程式就只是做事情而不用回傳)或這個 function 是要回傳計算值的(大部份應該都是這類)，要記住哦!