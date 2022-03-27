---
layout: post
title:  "JS 其實不是 class 的好朋友"
published: true
tags: 
  - ""
---

# 楔子
Coding Style 是一個很有意思的題目，往往不太一樣的 style 就會造成很不一樣的適應。

有趣的現象是在 js 環境下，後端很常用 OOP: class style。

但在 React 的環境下就很 function style，但在 angular 的世界內就也是 oop。

如果去面式 Node 的後端，如果是 full stack 前端比較熟悉 react 的情況下，

就可能要去準備一下 class style 的 JS/Node。

而在 backend 的夥伴要去應徵 React 的話，就也要熟悉 function style。

tl;dr

一開始入門 js 時，光搞懂這些 `this` `class` 的東西應該就飽了，

而市面上的書藉也大多數都是先講完這些原理再來開始寫程式，

感覺有點反過來了，寫程式總是先可以弄出東西再來慢慢理解為什麼，

而一開始的架構沒很大時，光搞抽象弄懂這些額外的「知識」，就搞的暈頭轉向了。

不妨先試著用純 functional 的方式來寫一些東西，慢慢真的熟悉了，

發現很多東西都開始好像 code 變的又臭又長時，再來解決這些抽象的東西，

反而會比較有感就感。

## Object.create
`Object.create` 就是建立一個 Object 的模版資料，會依 `prototype chian` 去尋找相關的 method
{% highlight javascript %}
var objTemplate = {name: 'yo'}
var childObj = Object.create(objTemplate)
console.log(childObj) // {} why?
console.log(childObj.name) // yo why?
{% endhighlight %}

## call apply bind
和 `Object.create` 有異取同工之妙
{% highlight javascript %}
function add(a, b) {
  return (this.age ? 0 : 10) + a + b
}
add.call({age: 10}, 1, 2) // 13
add.call(null, 1, 4) // 5 
add.apply(null, [1, 2]) // 3
add.apply(null, [1, 4]) // 5
var add1 = add.bind(null, 1);
console.log(add1(2)) // 3
console.log(add1(4))	
{% endhighlight %}

## Function
採用 Function style 來建立一些 OOP style 的感覺
{% highlight javascript %}
function Person(name){
  this.name = name
  this.getName = () => this.name
  getNameWithoutThis = () => this.name //?
}

var yo = new Person('yo')
yo.getNameWithThis() // yo

{% endhighlight %}

## class
### base class
{% highlight javascript %}
class Person {
  constructor(name) {
    this.name = name;
  }
  
  getName() {
    return this.name
  }
}

var yo = new Person('yo');
console.log(yo)
{% endhighlight %}

### extends
{% highlight javascript %}
class Person {
  constructor(name) {
    this.name = name;
  }
  
  getName() {
    return this.name
  }
}

// 使用 extends 
class Friend extends Person {
  constructor(name, age) {
    // 用 super 呼叫 extends 指定的 class
    super(name);
    this.age = age;
  }
  
  getAge() {
    return this.age
  }
}

const ho = new Friend('ho', 18);

console.log(ho.getName()); // ho
console.log(ho.getAge()); // 18
{% endhighlight %}

