---
layout: post
title:  "函數式編程 Functor 的運用"
published: true
tags: 
  - "javascript"
  - "fp"
---

# 特徵
- 可以把函數當參數使用
- 可以使用 compose(反向) 或 pipe(正向) 的串連方式

{% highlight javascript %}
// 建立一個標準的函數
let func = f = x => f(x)
let addOne = x => x + 1
let ff = func(addOne) // f = addOne
ff(1) // 1+1
{% endhighlight %}

`pipe` 就像一個水管一樣串接不同的 function

{% highlight javascript %}
const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x)
pipe(x => x + 1, x => x + 2)(3)
{% endhighlight %}

# 練習看看
有了上面的東西，如何使用?

如果有興趣可以去看看 [Dr.Boolean](https://egghead.io/courses/professor-frisby-introduces-composable-functional-javascript) 的一些影片，擷取一些常用的工具來做使用。


{% highlight javascript %}
const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x)
const I = x => x
const Box = x =>
({
  inspect: () => `Box(${x})`
})

Box(4).inspect //?
Box(4).inspect() //?
{% endhighlight %}

這邊有一個小小的坑，就要先熟悉到底現在的變數是「函數」還是「值」?

例如上面的 `Box(4).inspect` 這時是一個 function 所以需要執行完才會有回傳值，代表 `Box(3).inspect()` 是取它的值。

{% highlight javascript %}
Box(3).inspect // () => `Box(${x})`
Box(3).inspect() // Box(4)
{% endhighlight %}

那如果是 `Box(4).inspect(x => x + 1)` 呢?? 仔細看一下上面的

inspect: () => \`Box(${x})\`

他是一個不管任何參數的函數都回傳 `Box(x)`，所以答案就是 `Box(4)`

如果想練習看看，可以試著寫一個 Box 他有一個 `map` 的方法(function)，可以回傳一個 `Box(f(x))` 的概念。

答案:

{% highlight javascript %}
const Box = x =>
({
  map: f => Box(f(x)),
  inspect: () => `Box(${x})`
})
Box(3).map(x => x + 1) // 4
{% endhighlight %}
