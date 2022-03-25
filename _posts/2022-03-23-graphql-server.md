---
layout: post
title:  "GraphQL Server 方案"
published: true
tags: 
  - ""
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

# Apollor Server
`npm install apollo-server graphql`

## types 定義 schema
{% highlight javascript %}
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
{% endhighlight %}

## server 伺服服務

{% highlight javascript %}
// index.js
const { ApolloServer, gql } = require('apollo-server');

server.listen().then(({ url }) => {
  console.log(`🚀  Server ready at ${url}`);
})
{% endhighlight %}

## resolvers 羅輯

{% highlight javascript %}
const resolvers = {
  Query: {
    books: () => books,
  },
}
{% endhighlight %}

# Prisma Server

## types 定義 schema
![prisma types](https://i.imgur.com/jHkNjKU.png)

{% highlight javascript %}
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

{% endhighlight %}

## server 伺服服務

{% highlight javascript %}
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
    await prisma.$disconnect()
  })
{% endhighlight %}

## resolvers 羅輯

{% highlight javascript %}
query {
  postsConnection(where: { title_contains: "GraphQL" }) {
    aggregate {
      count
    }
  }
}
{% endhighlight %}
