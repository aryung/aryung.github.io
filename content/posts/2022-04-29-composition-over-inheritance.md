---
layout: post
title:  "composition over inheritance"
date: 2022-04-29T18:29:06+08:00
draft: false
tags: 
  - 'oop'
  - 'fp'
---
# 楔子
如果平常在寫 OOP(物件導向)語言的人，應該就會很熟悉繼承(inheritance);如果有寫 ruby 的人，應該更熟悉鴨子型別(duck typing);如果在寫 FP(functional programming)的，就更熟悉組合(composition)。每個語言都有他的特性，端看怎使用而以，語言的熟悉度就在於這些手法的習性而以。

但在 javascript 中其實都可以實踐這些特性來讓不同的技術人來使用，今天就拿一些網站的 demo code 來分享。

# code
先來看看標準的 es2015 的 classes & prototype

Inheritance with Classes

```javascript
// case I prototype
var Animal = function(name) {
  this.name = name
}

var Alligator = function(name) {
  Animal.apply(this, arguments); // Call parent constructor
}

Alligator.prototype = Object.create(Animal.prototype)
Alligator.prototype.constructor = Alligator
var jack = new Alligator("jack")

// case II classes
class Animal {
  constructor(name) {
    this.name = name
  }
}

class Alligator extends Animal {}

const jack = new Alligator("jack")
// extends
class Alligator extends Animal {
  constructor(...args) {
    super(...args)
  }
}
```

Object Composition

```javascript
const alligator = name => {
  const self = {
    name
  }

  return self
}

const jack =  alligator("jack")
```

這時把 class 實例化(new) 的 method 拆出來，用 function 來呈現，下面的例子就想像成 socialBehaviors 這個變數具有 sayHi eat poop 的功能。

或許有人會問，那 self 的屬性在哪使用了?其實在操作這樣的手法時，方法不就是處理這些屬性的結果值嗎?就是說在物件導向裡面的這些屬性值，其實就是要得到「方法」後的結果值，不是嗎?

```javascript
// We have some behaviors
const canSayHi = self => ({
  sayHi: () => console.log(`Hi! I'm ${self.name}`)
})
const canEat = () => ({
  eat: food => console.log(`Eating ${food}...`)
})
const canPoop = () => ({
  poop: () => console.log('Going to 💩...')
})

// Combined previous behaviours
const socialBehaviors = self => Object.assign({}, canSayHi(self), canEat(), canPoop())

const alligator = name => {
  const self = {
    name
  }

  const alligatorBehaviors = self => ({
    bite: () => console.log("Yum yum!")
  })

  return Object.assign(self, socialBehaviors(self), alligatorBehaviors(self))
}

const jack = alligator("jack")
jack.sayHi() // Hi! I'm jack
jack.eat("Banana") // Eating Banana...
jack.bite() // Yum yum!
```

如果把上面的例子抽出來 class 化就可以得到下面的模版

```javascript
const dog = name => {
  const self = {
    name
  }

  const dogBehaviors = self => ({
    bark: () => console.log("Woff woff!"),
    haveLunch: food => {
      self.eat(food)
      self.poop()
    }
  })

  return Object.assign(self, dogBehaviors(self), canEat(), canPoop())
}
```

## Composition with Javascript classes

```javascript
// Create a mixin
const FoodMixin = superclass => class extends superclass {
  eat(food) {
    console.log(`Eating ${food}`)
  }

  poop() {
    console.log("Going to 💩")
  }
}
```

這個是 extends 的情況

```javascript
class Animal {
  constructor(name) {
    this.name = name
  }
}

class Dog extends FoodMixin(Animal) {
  constructor(...args) {
    super(...args)
  }

  bark() {
    console.log("Woff woff!")
  }

  haveLunch(food) {
    this.eat(food)
    this.poop()
  }
}

const jack = new Dog("jack")
jack.haveLunch("little mouse")
```

## Combining Mixins

其實 Mixins 的概念就是所有的「方法」都是當「模組」來使用，然後所有的 class 就是依照想要的模組來繼承的概念，類似 duck typing，就假設如果有 bark() fly() eat() 三個方法塞在各自不同的模組，而如果有一個 class 想要有 fly 和 eat 就繼承這二個模組就可以了。

```javascript
const MixinA = superclass => class extends superclass {}
const MixinB = superclass => class extends superclass {}

class Base {}
class Child extends MixinB(MixinA(Base)) {}
```

可以一直巢狀使用 Mixins

```javascript
const MixinA = superclass => class extends superclass {}
const MixinB = superclass => class extends MixinA(superclass) {}

class Base {}
class Child extends MixinB(Base) {}
```

如果組合太多就會很巢狀...但其實可以轉成一個說法，如果把 MixinA 變成一個抽出來的方法，是不是就可以想像把一個 class 塞進去想要的方法。

```javascript
const MixinA = superclass => class extends superclass {}
const MixinB = superclass => class extends superclass {}
const MixinC = superclass => class extends superclass {}
const MixinD = superclass => class extends superclass {}

class Base {}
class Child extends MixinD(MixinC(MixinB(MixinA(Base)))) {}
```

這時其實可以利用 fp 常用的 `compose(lodash/fp/compose)` ，這時就想像把 Mixins 塞到某個 class 內。

```javascript
import compose from "lodash/fp/compose"

const MixinA = superclass => class extends superclass {}
const MixinB = superclass => class extends superclass {}
const MixinC = superclass => class extends superclass {}

class Base {}

const Behaviors = compose(MixinA, MixinB, MixinC)(Base)

class Child extends Behaviors {}
```

來標準化出來一個範本，拿個例子來看看

```javascript
// behaviors.js
export const EatMixin = superclass => class extends superclass {
  eat(food) {
    console.log(`Eating ${food}`)
  }
}

export const PoopMixin = superclass => class extends superclass {
  poop() {
    console.log("Going to 💩")
  }
}

export const FlyMixin = superclass => class extends superclass {
  fly() {
    console.log("Flying for real!")
  }
}
```

來實例化..

```javascript
import compose from "lodash/fp/compose"
import { EatMixin, PoopMixin, FlyMixin } from "./behaviors.js"

class Animal {
  constructor(name) {
    this.name = name
  }
}

const SuperPoweredDog = compose(EatMixin, PoopMixin, FlyMixin)(Animal)


class Dog extends SuperPoweredDog {
  bark() {
    console.log("Woff woff!")
  }

  haveLunch(food) {
    this.eat(food)
    this.poop()
  }
}

const jack = new Dog("jack")
jack.eat('doggie food') // Eating  doggie food
jack.bark(); // Woff woff!
jack.haveLunch("little mouse");  // // Eating little mouse. Going to 💩
```

# 參考文獻
[alligator inheritance over composition](https://alligator.io/js/class-composition/)