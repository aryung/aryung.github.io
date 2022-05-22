---
layout: post
title:  "Arrow function generic types"
date: 2022-05-22T09:30:06+08:00
draft: false
tags: 
  - "typescript"
  - "functional programming"
---
# 楔子
Arrow Function 很常使用，但它的 Generic Type 有點不太好記，列舉一下幾種

# Generic Arrow Functions
正常的函數 Generice Type 的格式

```typescript
function firstOrNull<T>(arr: T[]): T | null {
  return arr.length === 0 ? null : arr[0];
}
```

##  General
```typescript
const firstOrNull = <T>(
  arr: T[]
): T | null =>
  arr.length === 0 ? null : arr[0];
```


## Extends trick
```typescript
const firstOrNull = <T extends unknown>(
  arr: T[]
): T | null =>
  arr.length === 0 ? null : arr[0];
```

## Comma trick
```typescript
const firstOrNull = <T,>(
  arr: T[]
): T | null =>
  arr.length === 0 ? null : arr[0];
```

資料來源
- [Typescript function](https://www.typescriptlang.org/docs/handbook/2/functions.html)
- [arrow function generic type](https://www.carlrippon.com/generic-arrow-functions/)
