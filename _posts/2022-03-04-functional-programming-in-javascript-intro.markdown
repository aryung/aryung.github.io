---
layout: post
title:  "Functional-Programming javascript basic idea"
date:   2022-03-04 11:02:48 +0800
categories: javascript
---

基本的 `pipe` 實做

{% highlight javascript %}
const pipe = (...fns) => x => fns.reduce((y, f) => f(y), x)
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
