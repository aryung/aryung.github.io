---
layout: post
title:  "Lambda Calculus"
date: 2022-05-21T09:30:06+08:00
draft: false
tags: 
  - "javascript"
  - "functional programming"
---
# 楔子
這是一個在計算機歷史用數學演化的方式，具有代表性的規律。

# Lambda (λ) Calculus
## Function Abstraction
能夠用簡單的方式來呈現函數

> λx.x^2 + 1

用 js 來表示

```javascript
let res = x => x * x + 1
```

![](https://camo.githubusercontent.com/e0437f93c0ccd4f85327bb9bbe9d59b0d70f56513aeb8d722c2e7137f91871de/68747470733a2f2f7265732e636c6f7564696e6172792e636f6d2f74656368746865726961632f696d6167652f75706c6f61642f76313538373436363936372f6c616d6264612f6c616d6264616c616d5f7a71736a326d2e706e67)

## Function Application
何謂 β-reduction(beta reduction) ? 
就是把「值」代入取得最後的計算結果

![](https://camo.githubusercontent.com/de808a1db25d73f2bd4afeeed3bdd38b9d6872936a25807ac14130fa938969bd/68747470733a2f2f7265732e636c6f7564696e6172792e636f6d2f74656368746865726961632f696d6167652f75706c6f61642f76313538373436363936372f6c616d6264612f626574615f726564756374696f6e5f315f683772706f622e706e67)

```javascript
let res = (x => x * x + 1)(3)
```

但有些情況不符合 curry 的表示法

`λxy.x*y`

就應該轉換成

`(λx.(λy.x*y))`

但其實 ( ) 是可以被簡化的

`λx.λy.x*y`

如果先代入一個數值 5

`(λx.(λy.x*y)) (5)` = `(λy.5*y)`

再代入 7

`(λy.5*y)(7)` = 35

用 javascript 來表示

```javascript 
function product(x, y) {
  return x * y;
}

  product(5, 7) //retuns 35

// curry
function product (x) {
   return function (y) {
     return x * y;
   }
 }
```

參考資料
- [Lambda (λ) Calculus For Javascript Developers](https://gist.github.com/techtheriac/d0daa646b45fed7fba7c061bfc3154ee)
