---
layout: post
title:  "JS 原型說明 by Dan Abramov"
published: true
tags: 
  - "javascript"
---

# 楔子
不同的大神針對 JS 有不同的說明講解，來看一下 Redux 作者的說明。

Dan Abramov 有一個 [justjavascrpt prototype](https://justjavascript.com) 其實把觀念寫的蠻好的。

這篇文章是其中一個 prototype 的內容摘錄如下:

先有一個觀念就是 `a = '123'` 中 a 是變數(variable)， `123` 是值(value)
{% highlight javascript %}
// teeth 是 variable
// 32 是值
let human = {
  teeth: 32
}

let gwen = {
  age: 19
}
{% endhighlight %}

`human` 和 `gwen` 是一個 Object，他把屬性直接連到 `值`

![prototype-value](https://res.cloudinary.com/dg3gyk0gu/image/upload/v1590534071/just-javascript-email-images/jj09/prop.png)

{% highlight javascript %}
// gwen 沒有 teeth 屬性
console.log(gwen.teeth) // undefined
{% endhighlight %}

{% highlight javascript %}
let human = {
  teeth: 32
}
{% endhighlight %}

如果當用 `__proto__` 在 Object 裡面，`=`的右邊是一個變數，這是就是去找這個變數的 reference 在哪?

{% highlight javascript %}
let gwen = {
  // We added this line:
  // __proto__ 是 reference 
  __proto__: human,
  age: 19
}
{% endhighlight %}

![what is prototype](https://res.cloudinary.com/dg3gyk0gu/image/upload/v1590534071/just-javascript-email-images/jj09/proto.png)


{% highlight javascript %}
let human = {
  teeth: 32
}

let gwen = {
  // "Look for other properties here"
  __proto__: human,
  age: 19
}

console.log(gwen.teeth) // 32
{% endhighlight %}

作者有做一個動畫說明

![video](https://res.cloudinary.com/dg3gyk0gu/video/upload/f_auto/just-javascript-email-images/jj09/proto_anim-mp4.mp4)

{% highlight javascript %}
let human = {
  teeth: 32
};

let gwen = {
  __proto__: human,
  age: 19
};

console.log(human.age); // ?
console.log(gwen.age); // ?

console.log(human.teeth); // ?
console.log(gwen.teeth); // ?

console.log(human.tail); // ?
console.log(gwen.tail); // ?
{% endhighlight %}

再更複雜一下

{% highlight javascript %}
let mammal = {
  brainy: true,
};

let human = {
  __proto__: mammal,
  teeth: 32
};

let gwen = {
  __proto__: human,
  age: 19
};

console.log(gwen.brainy); // true
{% endhighlight %}

![img](https://res.cloudinary.com/dg3gyk0gu/image/upload/v1590534071/just-javascript-email-images/jj09/protochain.png)

# Shadow

如果自身有屬性，以自身屬性優先。

{% highlight javascript %}
let human = {
  teeth: 32
};

let gwen = {
  __proto__: human,
  // This object has its own teeth property:
  teeth: 31
};
{% endhighlight %}

{% highlight javascript %}
console.log(human.teeth); // 32
console.log(gwen.teeth); // 31
{% endhighlight %}

![img](https://res.cloudinary.com/dg3gyk0gu/image/upload/v1590534072/just-javascript-email-images/jj09/shadowing.png)

{% highlight javascript %}
console.log(human.hasOwnProperty('teeth')); // true
console.log(gwen.hasOwnProperty('teeth')); // true
{% endhighlight %}

# Assignment

重新指派值。

{% highlight javascript %}
let human = {
  teeth: 32
};

let gwen = {
  __proto__: human,
  // Note: no own teeth property
};

gwen.teeth = 31;

console.log(human.teeth); // ?
console.log(gwen.teeth); // ?
{% endhighlight %}

![](https://res.cloudinary.com/dg3gyk0gu/image/upload/v1590534072/just-javascript-email-images/jj09/step1.png)

{% highlight javascript %}
gwen.teeth = 31;
{% endhighlight %}

![img](https://res.cloudinary.com/dg3gyk0gu/image/upload/v1590534072/just-javascript-email-images/jj09/step2.png)

{% highlight javascript %}
console.log(human.teeth); // 32
console.log(gwen.teeth); // 31
{% endhighlight %}

# The Object Prototype

物件的一些特性屬性

{% highlight javascript %}
let obj = {};
console.log(obj.__proto__); // Play with it!
{% endhighlight %}

![img](https://res.cloudinary.com/dg3gyk0gu/image/upload/v1590534071/just-javascript-email-images/jj09/root1.png)

{% highlight javascript %}
let human = {
  teeth: 32
};
console.log(human.hasOwnProperty); // (function)
console.log(human.toString); // // (function)
{% endhighlight %}

# An Object With No Prototype

很特別的指定，如果指定的 null

{% highlight javascript %}
let weirdo = {
  __proto__: null
};
{% endhighlight %}

{% highlight javascript %}
console.log(weirdo.hasOwnProperty); // undefined
console.log(weirdo.toString); // undefined
{% endhighlight %}

# Polluting the Prototype

再複雜化一下各種情況

![img](https://res.cloudinary.com/dg3gyk0gu/image/upload/v1590534072/just-javascript-email-images/jj09/root2.png)

{% highlight javascript %}
let obj = {};
obj.__proto__.smell = 'banana';
{% endhighlight %}

{% highlight javascript %}
console.log(sherlock.smell); // "banana"
console.log(watson.smell); // "banana"
{% endhighlight %}

![img](https://res.cloudinary.com/dg3gyk0gu/image/upload/v1590534071/just-javascript-email-images/jj09/pollution.png)

# Fun Fact

prototype 就有些語法糖的方式

__proto__ vs. prototype

{% highlight javascript %}
function Donut() {
  return { shape: 'round' };
}

let donut = Donut();
{% endhighlight %}

{% highlight javascript %}
function Donut() {
  return { shape: 'round' };
}

let donutProto = {
  eat() {
    console.log('Nom nom nom');
  }
};

let donut1 = Donut();
donut1.__proto__ = donutProto;
let donut2 = Donut();
donut2.__proto__ = donutProto;

donut1.eat();
donut2.eat();
{% endhighlight %}

{% highlight javascript %}
function Donut() {
  this.shape = 'round';
}
Donut.prototype = {
  eat() {
    console.log('Nom nom nom');
  }
};

let donut1 = new Donut(); // __proto__: Donut.prototype
let donut2 = new Donut(); // __proto__: Donut.prototype

donut1.eat();
donut2.eat();
{% endhighlight %}

{% highlight javascript %}
class Donut {
  constructor() {
    this.shape = 'round';
  }
  eat() {
    console.log('Nom nom nom');
  }
};

let donut1 = new Donut(); // __proto__: Donut.prototype
let donut2 = new Donut(); // __proto__: Donut.prototype

donut1.eat();
donut2.eat();
{% endhighlight %}

# Why Does This Matter?

其實作者表示也很少使用原型鏈(其實自已也是..呵呵)，不過這應該是 framework 的差異性，

像 react 派走 functional programming ，而 angular 走的是 OOP 派，就是一種 coding style。

In practice, you probably won’t use prototypes in your code directly. (In fact, even using the __proto__ syntax is discouraged.)

Prototypes are unusual—most frameworks never embraced them as a paradigm. Still, you will notice prototypes hiding “beneath the surface” of other JavaScript features. For example, people often use prototypes to create a traditional “class inheritance” model that’s popular in other programming languages.

This became so common that JavaScript added a class syntax as a convention that “hides” prototypes out of sight. To see it in action, look at this snippet of a JavaScript class rewritten with __proto__ for a comparison.

Personally, I don’t use a lot of classes in my daily coding, and I rarely deal with prototypes directly either. However, it helps to know how those features build on each other, and it’s important to know what happens when I read or set a property on an object.
