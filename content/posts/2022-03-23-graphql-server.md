---
layout: post
title: 'GraphQL Server 方案'
date: 2022-03-23T18:29:06+08:00
draft: false
tags: 
  - "javascript"
---

# 楔子

GraphQL Server 的基本架構大概就幾個主要的元件:

- types 資料結構定義
- Server 伺服服務
- resolver 資料整理用

模版的東西就拿來改比較快，畢竟自已搞定也要很有愛。

[codesandbox](https://codesandbox.io/s/j1lkoy7nyv)

市面上常用的大概就 apollor server，要自已寫 types 和 resolvers。

另一個比較好用的有 prisma ，它本身有包含 database ORM 的功用，

也有自動產出 client 段方便套在自已的前端 web。

# 雜談

其實在 GraphQL 中，最重要的就是 types & resolvers，

這二個的意函在哪裡? types 其實就想像成資料的欄位設定，

resolvers 就想像成取得資料要進行「加減乘除」等等處理的方法。

如果從架構的角度來看

client(Framework) <-> (server <-> database)

在 client 和 server 端為了要彼此能溝通，勢必就要有一個標準的 types 來做依據。

就指的是 client 和 server 端其實都各自要能吃同樣的 types & 解析 query-languages

在 server 端，就因為常常有舊的資料庫問題，那是不是這個 type 同時也能滿足 ORM 的功能?

所以像 prisma 的服務就同時幫你產出 client.js & server 服務 & ORM 資料庫所需要的設定。

像 Apollo 就做 client + server 段，但 DB 端就用自已使用習慣的 ORM(eg. typeorm sequlized 之類的)

設定上因為有點麻煩，自然就有公司做服務省事賣錢...

# Apollo Server

`npm install apollo-server graphql`

## types 定義 schema

```
const typeDefs = gql`

# Comments in GraphQL strings (such as this one) start with the hash (#) symbol.

type Book {
title: String
author: String
}

# The "Query" type is special

type Query {
books: [Book]
}
`
```

## server 伺服服務

```
// index.js
const { ApolloServer, gql } = require('apollo-server');

server.listen().then(({ url }) => {
console.log(`🚀 Server ready at ${url}`);
})
```

## resolvers 羅輯

```
const resolvers = {
Query: {
books: () => books,
},
}
```

# Prisma Server

## types 定義 schema

![prisma types](https://i.imgur.com/jHkNjKU.png)

```
type Post {
id: ID! @unique
title: String!
published: Boolean!
author: User!
}

type User {
id: ID! @unique
age: Int
email: String! @unique
name: String!
accessRole: AccessRole
posts: [Post!]!
}

```

## server 伺服服務

```
import { PrismaClient } from '@prisma/client'

const prisma = new PrismaClient()

// A `main` function so that you can use async/await
async function main() {
const allUsers = await prisma.user.findMany({
include: { posts: true },
})
// use `console.dir` to print nested objects
console.dir(allUsers, { depth: null })
}

main()
.catch((e) => {
throw e
})
.finally(async () => {
await prisma.\$disconnect()
})
```

## resolvers 羅輯

```

# Count all posts with a title containing 'GraphQL'

query {
postsConnection(where: { title_contains: "GraphQL" }) {
aggregate {
count
}
}
}
```
