---
layout: post
title:  "JS 其實不是 class 的好朋友"
date: 2022-03-27T18:29:06+08:00
draft: false
tags: 
  - "javascript"
---

# 楔子
Coding Style 是一個很有意思的題目，往往不太一樣的 style 就會造成很不一樣的適應，有趣的現象是在 js 環境下，後端很常用 OOP: class style. 

但在 React 的環境下就很 function style，但在 angular 的世界內就也是 oop。如果去面式 Node 的後端，如果是 full stack 前端比較熟悉 react 的情況下， 就可能要去準備一下 class style 的 JS/Node。

而在 backend 的夥伴要去應徵 React 的話，就也要熟悉 function style。

tl;dr

一開始入門 js 時，光搞懂這些 `this` `class` 的東西應該就飽了，而市面上的書藉也大多數都是先講完這些原理再來開始寫程式，感覺有點反過來了，寫程式總是先可以弄出東西再來慢慢理解為什麼，而一開始的架構沒很大時，光搞抽象弄懂這些額外的「知識」，就搞的暈頭轉向了。

不妨先試著用純 functional 的方式來寫一些東西，慢慢真的熟悉了，發現很多東西都開始好像 code 變的又臭又長時，再來解決這些抽象的東西，反而會比較有感就感。當看了下面一堆 js 的說明，光考就弄倒一堆人了，就自然討厭 js 了，但就算弄懂其實..實務上的差異並不太大，踩到坑再去記住會身體更有感覺。

通常 `this` 會和 `class` 一起使用，而其實`this`就把它當成`context`(環境參數)來看待就可以了。

## Object.create
`Object.create` 就是建立一個 Object 的模版資料，會依 `prototype chian` 去尋找相關的 method
```
var objTemplate = {name: 'yo'}
var childObj = Object.create(objTemplate)
console.log(childObj) // {} why?
console.log(childObj.name) // yo why?
```

## this 的好朋友: call apply bind

和 `Object.create` 有異取同工之妙

這篇可以參考 [pjchender](https://pjchender.blogspot.com/2016/06/function-borrowingfunction-currying.html)

```
// this 指的是 function scope
var person = {
  firstname: 'Jeremy',
  lastname: 'Lin',
  getFullName: function(){
    var fullname = this.firstname + ' ' + this.lastname;
    return fullname;
  }
}

// this 指是 ? 因為沒有 object 就往上一層 window
var logName = function(location1,location2){
  console.log('Logged: ' + this.getFullName());
  console.log('Arguments: ' + location1 + ' ' + location2);
}
```

有了上面的樣本，開始把 this 用 `bind` `apply` `call` 綁進去取代 `this`，正確來說是，這類的函數，把它想像第一個參數就是綁定`this`的概念，之後就是其它函數的變量。

這樣講蠻難體會的吧?就來舉個例子吧

```
function getThis(){
  console.log(this)
  console.log(this?.name)
}

getThis() // undefined 或 window

getThis.bind({name: 'ac'})() // 'ac'

// 如果加入其它的參數
function getThisArg(age){
  console.log(this)
  console.log(this?.name)
  consol.log(age)
}
getThisAge(13) // undefined 或 window
getThis.bind({name: 'ac'})(99) // 'ac'
// 參數用 array 傳入
getThis.apply({name: 'ac'}, [99]) // 'ac' 99 
// 參數用 argvs 傳入
getThis.call({name: 'ac'}, 99) // 'ac' 99

```

再拿其它網站的例子來參考 [pjchender 筆記](https://pjchender.blogspot.com/2016/06/function-borrowingfunction-currying.html)

```
var logName = function(location1,location2){
  console.log('Logged: ' + this.getFullName());
  console.log('Arguments: ' + location1 + ' ' + location2);
}.bind(person) // 重點是取 person 的 this.getFullName()

logName('Taiwan', 'Japan')
```

來看看 call & apply

```
var logName = function(location1,location2){
  console.log('Logged: ' + this.getFullName());
  console.log('Arguments: ' + location1 + ' ' + location2);
}
// call
logName.call(person, 'Taiwan', 'Japan')

var logName = function(location1,location2){
  console.log('Logged: ' + this.getFullName());
  console.log('Arguments: ' + location1 + ' ' + location2);
}
// apply
logName.apply(person, ['Taiwan', 'Japan'])
```

```
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
```

## Function
採用 Function style 來建立一些 OOP style 的感覺
```
function Person(name){
  this.name = name
  this.getName = () => this.name
  getNameWithoutThis = () => this.name //?
}

var yo = new Person('yo')
yo.getNameWithThis() // yo

```

## class
### base class
```
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
```

### extends
```
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
```

### arrow function vs function (this)

箭頭函數的 this 取最近一層的 scope，所以在 function 內的 this 的 scope 在 function，而 setTimeout 的 scope 會取最大層(window)

```
let id = 21;
let data = {
  id: 21,
};

fn.call(data);
arrowFn.call(data);

// 原本的 function
function fn() {
  console.log(this.constructor.name); // Object(data)

  setTimeout(function () {
    console.log(this.constructor.name); // Window
  }, 100);
}

// 箭頭函式 Arrow function
function arrowFn() {
  console.log(this.constructor.name); // Object(data)

  setTimeout(() => {
    console.log(this.constructor.name); // Object(data)
  }, 100);
}
```

這時的 arrowFn 指的是 window

```
var button = document.querySelector('button')
var arrowFn = () => {
  // 建立 function 時 this 指 Window
  console.log(this.constructor.name) // 執行 function 時 this 指 Window
}
var fn = function () {
  // 建立 function 時 this 指 Window
  console.log(this.constructor.name) // 執行 function 時 this 指 HTMLButtonElement
}

button.addEventListener('click', arrowFn)
```