---
layout: post
title:  "Hight Order Function & Callback Function"
published: true
tags: 
  - "functional programming"
---

# 楔子
最典型的使用案例是 [jQuery](https://jquery.com) 時代

{% highlight javascript %}
function add (x,y) {
  return x + y
}

function addFive (x, referenceCallback) {
  return referenceCallback(x)
}

addFive(10,add)

// 標準化
function highOrderFunction (x, callback) {
  return callback(x)
}

// jQuery ($)
jQuery('#btn').on('click', () => {
  console.log('callback everywhere')
})

{% endhighlight %}
