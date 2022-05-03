---
layout: post
title:  "more about prototypes"
date: 2022-05-01T18:30:06+08:00
draft: false
tags: 
  - 'oop'
  - 'prototype'
---
# 楔子
原型鏈 (prototype chain) 應該是 javascript 新人入門最討厭的東西了。

在這個語言中其實只有幾大類別(number string boolean)
- Undefined (undefined), used for unintentionally missing values.
- Null (null), used for intentionally missing values.
- Booleans (true and false), used for logical operations.
- Numbers (-100, 3.14, and others), used for math calculations.
- BigInts (uncommon and new), used for math on big numbers.
- Strings ("hello", "abracadabra", and others), used for text.
- Symbols (uncommon), used to perform rituals and hide secrets.

比較特別的就是 Object 和 Function 了，先摘錄一些網路大神的圖解。

# 原型
每個 function 都有一個 `prototype`
每個 instance 都有一個 `__proto__`
function 是 Function 的 instances
object 是 Object 的 instances
![](https://res.cloudinary.com/dg3gyk0gu/image/upload/v1578948516/just-javascript-email-images/jj02/universe.png)

## 名詞定義
function Person (){} 構造函數(constructor)
var p = new Person 實例對象(instance)
原型對象(prototype):每個構造函數都有一個自已的原型對像(看不到)
constructor: 函數內有一個 constructor 屬性會定義到 prototype

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/5/3/16a798e975b1f12b~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.image)


![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/5/3/16a7ca56f543da9c~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.image)


## 名詞彼此關係與定義
- 原型其實就是一個對象，存在於內存中，我們看不見
- 原型可以給構造函數的實例對象提供方法
- `構造函數.prototype` 和 `實例對象.__proto__` 都可以訪問到原型對象
- 有prototype屬性的是構造函數，有__proto__屬性的是實例對象
- 函數的prototype屬性: 在定義函數時自動添加的, 默認值是一個空Object對象
- 對象的__proto__屬性: 創建對象時自動添加的, 默認值為構造函數的prototype屬性值
- 程式員能直接操作顯式原型, 但不能直接操作隱式原型(ES6之前)

資料來源
- [JustJavascript](https://justjavascript.com)
- [prototype](https://juejin.cn/post/6975694337698955295)
- [another prototype](https://juejin.cn/post/6844903837623386126)