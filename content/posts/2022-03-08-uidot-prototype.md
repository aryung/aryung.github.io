---
layout: post
title:  "JS Prototype by Tyler McGinnis"
date: 2022-03-08T21:29:06+08:00
draft: false
tags: 
  - "javascript"
---

# 楔子
Tyler 講的 prototype 的觀念蠻清楚 & 簡單的，稍做一下記錄
[Tyler McGinnis javascript](https://www.youtube.com/watch?v=XskMWBXNbp0)
## prototype

> Prototype is a property of a function that point to an object when function is created.

用下面的例子就可以來解釋上面的句子

```
function imAFunction () {}
imAFunction.prototype // {constructor: f}
```

把共用的 Method 放在一起包裝在一個 Object 內

```
// 共用的 method
const animalMethods() {
  eat () {},
  sleep () {},
  play () {}
}

function Animal (name, energy) {
  let animal = Object.crete(animalMethods)
  animal.name = name
  animal.energy = energy

  return animal
}
```

# 關鍵字 new

`new` 做的就只是把 Object.create 做修正一下

```

function AnimalWithNew (name, energy) {
  this = Object.create(Animal.prototype)
  this.name = name
  this.energy = energy

  return this
}

let snoppy = new AnimalWithNew('snoopy', 100)

```
