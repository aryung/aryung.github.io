---
layout: post
title:  "Functional-Programming javascript basic idea"
published: true
# categories: javascript fp
tags: 
  - "javascript"
  - "fp"
# comments: true
---

基本的 `pipe` 實做

{% highlight javascript %}
const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x)
{% endhighlight %}

