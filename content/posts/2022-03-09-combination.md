---
layout: post
title:  "取得所有的組合再求結果"
date: 2022-03-09T21:29:06+08:00
draft: false
tags: 
  - "演算法"
---
# 使用情境
- 資料中任二組(或以上)符合某個條件

  例如: 想找 [1,2,3,4,5] 任二個值「大於」3有哪些情況

- 二組資料的「所有組合」想找符合條件的結果
  
  例如: [1,2,3,4] 和 [7,8.9] 的組合中那個「乘」大於 10

上面的情況在整理資料中蠻常發現的，所以手上有這個函數是蠻方便的，也不太需要自已去造輪子刻，拿來用就好了。

這種會分二種情況:第一種是自已資料的展開，第二種是給二個資料組合展開。

# Combination

```
const choose = (n, xs) =>
  n < 1 || n > xs .length
    ? []
    : n == 1
      ? [...xs .map (x => [x])]
      : [
          ...choose (n - 1, xs .slice (1)) .map (ys => [xs [0], ...ys]),
          ...choose (n , xs .slice (1))
        ]

const getCombs = (min, max, xs) => 
  xs .length == 0 || min > max
    ? [] 
    : [...choose (min, xs), ...getCombs (min + 1, max, xs)]

getCombs (0, 3, [1, 2, 3])
// [[1], [2], [3], [1, 2], [1, 3], [2, 3], [1, 2, 3]] 
getCombs (2, 2, [1, 2, 3])
// [[1, 2], [1, 3], [2, 3]]
```

想找 [1,2,3,4,5] 任二個值「大於」3有哪些情況 ?

如果我們有任意二個的組合，再過一個過濾的方法(function)就可以取得答案。

```
getCombs (2, 2, [1, 2, 3, 4, 5]).filter((data) => data[0] + data[1] > 3)
// 等同於 記的 filter 這個 function 要回傳 true/false
function checkValidBigThan3 (v) {
  return  v[0] + v[1] > 3 ? true : false
}
getCombs (2, 2, [1, 2, 3, 4, 5]).filter(checkValidBigThan3)
// 上面的 code 有些狀況會噴，以後再來改
```

# 取解 

為何叫 Product ? 因為他很像購物車的產品感覺，單價 x 個數的觀念，全部產出來。

例如: [1,2,3,4] 和 [7,8.9] 的組合中那個「乘」於於 10 ? 

試想一下，把所有的組合先展開，再相乘，再過濾(>10)

來運用一下 Ramda [Xprod](https://ramdajs.com/docs/#xprod)
```
R.xprod([1, 2], [3, 4])
 .map(list => list[0] * list[1])
 .filter(val => val > 10)
```

