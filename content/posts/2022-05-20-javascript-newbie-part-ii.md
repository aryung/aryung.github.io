---
layout: post
title:  "寫給想跳坑的 JS 新手(Part II): coding style"
date: 2022-05-20T09:30:06+08:00
draft: false
tags: 
  - "javascript"
  - "functional programming"
---
# 楔子
上一篇提到了 map filter reduce，這三個是最常見的也是最常用的使用方法，為了讓「程式碼」易讀，可以改造一下長相方便讓人看的懂在做什麼，至於怎做的就交給專業的人去優化改善就好。

所以先至少讓別人看的懂自已在寫的東西優先，試想看看，如果你是看 code 的人，「看的懂」vs 「要花一點點時間去細看」這二種，哪一種比較好讀? 先做到至少菜端出來看起來想吃，至於好不好吃就再說囉。嘿嘿嘿

# 程式碼的味道
用個最簡單的例子，把一個數字 x3 之後再 +1，但當數字是 5時就返回原值

f(1) = 1 * 3 + 1

f(5) = 5
來寫出這個 f 但我們用不同的長相來看看

## 撒尿牛丸全部攪在一起
這個應該是大部份的程式羅輯，就是把資料拆開來一個一個處理。

```
function f(x) {
   if(x !== 5) {
      return 3 * x + 1
   } else {
      return x
   }
}
```

## 點點點到天邊
這個 coding-style 就像你打開一個箱子把東西拿出來，「動作」後再放回去，依序處理。
```
let box = x => ({f: f => box(f(x)), x})
box(1)
  .f(multi3)
  .f(add1) // {f: ƒ f(), x: 4}
box(5)
  .f(multi3)
  .f(add1) // {f: ƒ f(), x: 6}

// 運算的 function 參考用
function multi3(x) {
  if(x !== 5) {
    return 3 * x
  } else {
    return x
  }
}

function add1(x) {return x+1}
```

## 按造步驟一步一步寫
這個版本和上面的版本意思差不多，就是把資料塞進去，接下來塞一堆要處理的功能。(不過也有另一派是把資料放最後，這個以後有空再討論囉)
```
let box = x => f => g => f(g(x))
box(1)(multi3)(add1) // 4
box(5)(multi3)(add1) // 6

// 運算的 function 參考用
function multi3(x) {
  if(x !== 5) {
    return 3 * x
  } else {
    return x
  }
}

function add1(x) {return x+1}
```
