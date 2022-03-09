---
layout: post
title:  "JS __proto__ by Dan Abramov"
published: true
tags: 
  - "javascript"
---

# 楔子
[justjavascrpt prototype](https://justjavascript.com)

{% highlight javascript %}
let human = {
  teeth: 32
};

let gwen = {
  age: 19
};
{% endhighlight %}

![prototype-value](https://res.cloudinary.com/dg3gyk0gu/image/upload/v1590534071/just-javascript-email-images/jj09/prop.png)

{% highlight javascript %}
console.log(gwen.teeth); // undefined
{% endhighlight %}

{% highlight javascript %}
let human = {
  teeth: 32
};

let gwen = {
  // We added this line:
  __proto__: human,
  age: 19
};
{% endhighlight %}

![what is prototype](https://res.cloudinary.com/dg3gyk0gu/image/upload/v1590534071/just-javascript-email-images/jj09/proto.png)

# Prototypes in Action

{% highlight javascript %}
let human = {
  teeth: 32
};

let gwen = {
  // "Look for other properties here"
  __proto__: human,
  age: 19
};

console.log(gwen.teeth); // 32
{% endhighlight %}

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

# The Prototype Chain

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

# Shadowing

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
In practice, you probably won’t use prototypes in your code directly. (In fact, even using the __proto__ syntax is discouraged.)

Prototypes are unusual—most frameworks never embraced them as a paradigm. Still, you will notice prototypes hiding “beneath the surface” of other JavaScript features. For example, people often use prototypes to create a traditional “class inheritance” model that’s popular in other programming languages.

This became so common that JavaScript added a class syntax as a convention that “hides” prototypes out of sight. To see it in action, look at this snippet of a JavaScript class rewritten with __proto__ for a comparison.

Personally, I don’t use a lot of classes in my daily coding, and I rarely deal with prototypes directly either. However, it helps to know how those features build on each other, and it’s important to know what happens when I read or set a property on an object.
