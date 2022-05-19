---
layout: post
title:  "寫給想跳坑的 JS 新手(Part I): map filter reduce"
date: 2022-05-19T09:30:06+08:00
draft: false
tags: 
  - "javascript"
  - "functional programming"
---
# 楔子
身邊有些朋友有不少會想試著寫寫程式，常常坊間書翻一翻看到物件的東西，很快的就把書放下了，慢慢的也就失去興趣了。

但其實怎讓這些朋友開始可以有些動手做也能跑出一些結果，那種成就感會讓人慢慢的親近寫程式(coding)，不可否認物件導向語言統治了軟體界，但光要先學會這個觀念可能就讓人却步了。

以前有部電影「張三峰」有句台詞，怎「張無忌」問，怎學會這部武功?「張三峰」說:「忘掉就好了」。

其實函數式語言的「入門」蠻簡單的，就像小時候的數學，函數吃一些變數，跑出來一個結果，用這個方式寫寫東西就可以做一些小實務的作品，也很有成就感。

就讓我們試試吧..

# Declarative 宣告式 vs. Imperative 命令式
命令式(Imperative)白話文就是要用電腦的角度去思考，怎把答案做出來。

如果相要把一連串的資料去掉一些特定值(前提條件就要知道何謂 for-loop)，示範的程式碼大概會長成下面的樣子

```
function filter(array) {
  let newArray = []
  for (let index = 0; index < array.length; index++) {
    const element = array[index]
    if (element !== null && element !== undefined) {
      newArray[newArray.length] = element
    }
  }
  return newArray
}
// use case:
filter([0, 1, undefined, 2, null, 3, 'four', ''])
// [0, 1, 2, 3, 'four', '']
```

但如果是所謂的宣告(Declarative)式，就是使用 filter 函數(知道 map/filter/reduce..)，知道 filter 是吃一個「判斷(predicate)」的方法，細部的執行不太需要去了解，程式碼大概就會長的像下面的例子

```
function predicate(element) {
  // 判斷示 回傳 true or false
  return (element !== 3)
}

[0,1,2,3,4,5].filter(predicate)
// [0,1,2,4,5]
```

也可以用 map 來解決這個問題，map 的觀念就是每個東西拿出來檢查，檢查完再放回去(要看做記號或處理之類的)，map 也是吃一個函數(要做的事)，比較特別的是當條件 3 時要回傳 0，它不像 filter 是過濾完的結果，map 是檢查完都有回傳值。

```
function predicate(element) {
  // 判斷示 要做什事 要回傳什麼
  if(element !== 3) {
    return element
  } else { 
    return 0
  }
}

[0,1,2,3,4,5].map(predicate)
// [0,1,2,0,4,5]
```

那如果想要把所有的資料加總呢?就使用 reduce，這個方法有點像捍麵一樣，隨著每個數值桿，再和前面上次「桿完」的數值做處理。

```
// 0 為初始值，acc 為上次運算的結果，cur 為這次的運算值
[1,2,3,4].reduce((acc, cur) => {
  return acc + cur
}, 0)
// 10
```

上面的三種方式可以用一張圖來表示

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/d1lu2b5k4k9pd9oq93z6.jpeg)
