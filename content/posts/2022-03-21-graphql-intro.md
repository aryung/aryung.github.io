---
layout: post
title:  "graphql use case"
date: 2022-03-21T18:29:06+08:00
draft: false
tags: 
  - "javascript"
---
# 楔子
一般傳統的 RESTFul API 通常有一個缺點， 就是要設定不同的路由(routes)來當作入口點， 久而久之就會有一堆的路由網址，也不敢去做任何的修正， 誰敢擔責任改了是不是會噴錯， 慢慢的就也放著讓它愈長愈大了，當然後來有些 library 像 swagger 可以幫助自動產生文件， 但也還是需要做些設定，這也慢慢的讓 graphQL 有開始長大的空間。

# GraphQL
主要的精神架構為， Server 設定好條件(資料的格式 schema & 路由 routes)，

這時透過查詢語法(Query Language)所帶來的參數，經由 business logic 的轉換(資料 CRUD)再回拋資料。

## Server 元件
要架一個 GraphQL Server，思考一下要什麼東西??

就觀念上就要有三個東西 = Server + 資料格式 Schema + 一些數據的處理
- Server: 可以用 ExpressJS，Apollo Server.. [graphQL server](https://graphql.org/code/#javascript)
- Scehma: 基本上就是一個定義檔，可以用不同語言的格式 [type & schema](https://graphql.org/learn/schema/#object-types-and-fields)，應該各語言都有一些轉換方式
- logic: 通常就會牽涉到「數據」的處理，就會扯到 DB ，就會有一些 ORM 使用(eg. [sequelize](https://sequelize.org), typeorm, prisma..等等)

### server
```typescript
var express = require('express')
var { graphqlHTTP } = require('express-graphql')
var { buildSchema } = require('graphql')

var schema = buildSchema(`
  type Query {
    hello: String
  }
`)

var root = { hello: () => 'Hello world!' }

var app = express()
app.use('/graphql', graphqlHTTP({
  schema: schema,
  rootValue: root,
  graphiql: true,
}))
app.listen(4000, () => console.log('Now browse to localhost:4000/graphql'))
```

### 定義(Schema)
GraphQL 的 schema 定義其實很像 typescript(或許有小夥伴說這是什?以後再做介紹囉)

```typescript
type Character {
  name: String!
  appearsIn: [Episode!]!
}
```

### 查詢語法(Query Language)
有 GUI 的查詢語法

```typescript
{
  hero {
    name
    appearsIn
  }
}
```

### 配合查詢語法所用的 resolver(business logic)
類似說，當查詢語法進來時，會有一些變數(ctx, parent...)，這時吃到這些參數時，

所進行的一連串資料的處理(eg. ORM 拉資料 > 做處理 > 吐回資料)

```typescript
var postType = new GraphQLObjectType({
  name: ‘Post’,
  fields: {
    body: {
      type: GraphQLString,
      resolve: (post, args, context, { rootValue }) => {
        // return the post body only if the user is the post's author
        if (context.user && (context.user.id === post.authorId)) {
          return post.body
        }
        return null
      }
    }
  }
})
```