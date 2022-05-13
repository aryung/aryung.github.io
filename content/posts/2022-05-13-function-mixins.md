---
layout: post
title:  "function mixins"
date: 2022-05-13T09:30:06+08:00
draft: false
tags: 
  - "javascript"
  - "functional programming"
---

# 楔子
在公司的專案開發中，程式總會愈長愈大，會慢慢的到了一個程度難以理解，在開發的過程中也慢慢開始把程式做一些模組化的動作。

在程式的抽像化過程中，應該有二大派系很當使用，就是物件導向 & 函數式編程。通常會蠻常出現二個關鍵字
- 繼承(inheritance)
- 組合(composition)


# Mixins

> “Favor object composition over class inheritance” the Gang of Four, “Design Patterns: Elements of Reusable Object Oriented Software”

有一種說法所謂的 mixins 就是像冰淇淋甜筒一樣，想吃什麼挖什麼口味，只要甜筒有那個味道功能，甜筒就只是一個載具而以。

# Object composition

在 javascript 內，物件可以塞 function ，這時就要利用到 `Object.assing` 這個方法。

```javascript
const chocolate = { 
  hasChocolate: () => true
}

const caramelSwirl = { 
  hasCaramelSwirl: () => true
}

const pecans = { 
  hasPecans: () => true
}

const iceCream = Object.assign(
  {}, 
  chocolate,
  caramelSwirl,
  pecans
)

console.log(`
	  hasChocolate: ${ iceCream.hasChocolate() }
	  hasCaramelSwirl: ${ iceCream.hasCaramelSwirl() }
	  hasPecans: ${ iceCream.hasPecans() }
  `)
//  hasChocolate: true
//  hasCaramelSwirl: true
//  hasPecans: true
```

上面的 `iceCream` 就取了各物件的方法拼湊而成，就想像甜筒可以塞不同口味的東西。

# Function inheritance

如果採用物件的方式，要實現同樣的功能，最常用的就是繼承了，實做一個物件的東西就要一層繼承一層來實現。

```javascript
// Base Object Factory
function base(spec) {
  var that = {}
  that.name = spec.name
  return that
}

//Construct a childobject, inheriting from "base"
function child(spec){
// Create the object through the "base" constructor 
  var that = base(spec)
  that.sayHello = function(){
	  return 'Hello, I\'m ' + that.name
	 }
	return that
}
//Usage
var result = child({name:'a functional object'})

console.log(result.sayHello()) //'Hello,I'm a functional object
```

來比較而言，就繼承來看是蠻繁瑣的，但只是用這個角度的例子來看而以，也不用全盤去否定。

# Function composition

如果再把上面的物件組合來再往前一步看，可以用一個物件(object)不要擴大去想成物件導向，就是把多個方法塞到一個物件當做要加入的一群方法。

```javascript
const flying = o => {
  let isFlying = false
  return Object.assign(
    {}, 
    o, 
    {
      fly() {
        isFlying = true
        return this
      },
      isFlying: () => isFlying,
      load() {
        isFlying = false
        return this
      }
    }
  )
}

const bird = flying({})
console.log( bird.isFlying()) // false
console.log( bird.fly().isFlying()) // true
```

這個例子就是 quacking 是想像成他吃二個參數，第一個是要加新東西的 base 物件，再給它塞入 quack 這個方法。

```javascript
const quacking = quack => o => Object.assign({}, o, {
  quack: () => quack
})

const quacker = quacking('Quack!')({})
console.log(quacker.quack()) // 'Quack'
```

上面的 `Quack!` 甚至都可以改成 function 其實就是 quack='Quack' 的意思，而在 javascript 內可以把 function 當成 argument 傳入。

# Composing Functional Mixins

再把它標準化一下使用的模版。

```javascript
const createDuck = quack => quacking(quack)(flying({}))
const duck = createDuck('Quack!')
console.log(duck.fly().quack())
```

組合一下使用 pipe 的方式來隨意組合想用的 functionS

```javascript
const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x)
const createDuck = quack => pipe (
  flying,
  quacking(quack),
)({})
const duck = createDuck('Quack!!')
console.log(duck.fly().quack())
```


參考資料
- [Favor object composition over class inheritance](https://www.amazon.com/Design-Patterns-Elements-Reusable-Object-Oriented/dp/0201633612/ref=as_li_ss_tl?ie=UTF8&qid=1494993475&sr=8-1&keywords=design+patterns&linkCode=ll1&tag=eejs-20&linkId=6c553f16325f3939e5abadd4ee04e8b4)
- [Composing Software by Eric Elliott](https://medium.com/javascript-scene/composing-software-the-book-f31c77fc3ddc)