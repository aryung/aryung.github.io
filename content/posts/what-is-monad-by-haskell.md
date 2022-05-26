---
layout: post
title:  "what is monad by Haskell"
date: 2022-05-26T18:29:06+08:00
draft: false
tags: 
  - 'haskell'
---

# 楔子
Monad 是一個數學特性，來看看純的 Haskell 語言怎看待，使用「除法」來做一個簡單的示範說明。

# Monads
這是一個 Haskell 基本的語法，宣告一個 Expre 的格式，他有可能是 Int (Val type) ，也有可能是 Div
```haskell
data Expr = Val Int | Div Expr Expr
eval :: Expr -> Int
eval (Val n) = n
eval (Div x y) = eval x `div` eval y
eval (Div (Val 1) (Val 0))
```

```haskell
safediv :: Int -> Int -> Maybe Int safediv _ 0 = Nothing
safediv n m = Just (n ‘div‘ m)
```

```haskell
eval :: Expr -> Maybe Int eval (Val n) = Just n
eval (Div x y) = case eval x of Nothing -> Nothing
                      Just n -> case eval y of
                      Nothing -> Nothing Just m -> safediv n m
```

```haskell
eval :: Expr -> Maybe Int
eval (Val n) = pure n
eval (Div x y) = pure safediv <*> eval x <*> eval y
```

```haskell
(>>=) :: Maybe a -> (a -> Maybe b) -> Maybe b
mx >>= f = case mx of
                Nothing -> Nothing
                Just x -> f x
```

```haskell
eval :: Expr -> Maybe Int
eval (Val n) = Just n
eval (Div x y) = eval x >>= \n ->
              eval y >>= \m -> 
              safediv n m
```

```haskell
m1 >>= \x1 -> m2 >>= \x2 -> 
.
.
mn >>= \xn -> f x1 x2 ... xn
```


```haskell
do x1 <- m1 x2 <- m2
.
.
.
xn <- mn
f x1 x2 ... xn
```


```haskell
eval :: Expr -> Maybe Int
eval (Val n) = Just n
eval (Div x y) = do n <- eval x
              m <- eval y
              safediv n m
```

```haskell
class Applicative m => Monad m where 
  return :: a -> m a
  (>>=) :: m a -> (a -> m b) -> m b
  return = pure
```

```haskell
return :: Applicative f => a -> f a 
return = pure
```


資料來源
[What is Monad](https://www.youtube.com/watch?v=t1e8gqXLbsU&t=1178s)
