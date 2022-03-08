---
layout: post
title:  "Javascript Prototype"
published: true
tags: 
  - "javascript"
---

# 楔子

> Prototype is a property of a function that point to an object when function is created.

{% highlight javascript %}
function imAFunction () {}
imAFunction.prototype // {constructor: f}
{% endhighlight %}

# prototype

easy to share methods.

{% highlight javascript %}
const animalMethods() {
  eat () {},
  sleep () {},
  play () {}
}

const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x)
const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x)
{% endhighlight %}

# Keyword New

{% highlight javascript %}
function Animal (name, energy) {
  let animal = Object.crete(Animal.prototype)
  animal.name = name
  animal.energy = energy

  return animal
}

function AnimalWithNew (name, energy) {
  // this = Object.cjreate(Animal.prototype)
  this.name = name
  this.energy = energy

  return this
}

Animal.prototype.eat = function (amount) {}
Animal.prototype.sleep = function (amount) {}
Animal.prototype.play = function (amount) {}

{% endhighlight %}
