---
layout: post
title:  "Hight Order Function & Callback Function"
published: true
tags: 
  - "functional programming"
---

# 楔子
最典型的使用案例是 [jQuery](https://jquery.com) 時代，標準的 jQuery 格式大概就長的像下面這樣的格式

{% highlight javascript %}
// jQuery === $
var hiddenBox = $("#banner-message")
jQuery("#button-container button").on("click", function(event) {
  hiddenBox.show()
})
{% endhighlight %}

通常簡化來看就是 $(DOMelement, clickFunction)，而 clickFunction 的長相像就也是像 
{% highlight javascript %}
function (event) {
  // event handling
}
{% endhighlight %}

如果是把這種格式轉換一下，大概有幾個注意的要點:
- 參數可以是一個函數
- 函數以一個參數為佳

{% highlight javascript %}
function add (x,y) {
  return x + y
}

function addFive (x, referenceCallback) {
  return referenceCallback(x)
}

addFive(10, add)

// 標準化
function highOrderFunction (x, callback) {
  return callback(x)
}
{% endhighlight %}

蠻多的 js 的函數也都採用這樣的 coding style。
