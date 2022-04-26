---
layout: post
title:  "Neo4j: 圖資料庫"
date: 2022-03-05T18:29:06+08:00
draft: false
tags: 
  - "database"
---
# 楔子
平常使用的最多的應該是 MySQL 了，但通常不同的資料庫有它的使用情境。

簡單分就是有 `SQL` 和 `NoSQL` 系列，就二分法的方式處理。

各種資料庫都有它的好用的地方，像 NoSQL 比較有名的就 [Redis](https://redis.io) (它有硬碟版 [SSDB](https://ssdb.io))

資料的結構常常會依據使用情境不同有所適合的資料庫。

今天來分享所謂的「圖型」資料庫，資料庫一開始比較需要掌握的就是「設計的理念」

最具代表性的就是 [Neo4j](https://neo4j.com)

先來看一下它查詢的語法就比較容易理解他的概念。

```
MATCH (n {name: 'John'})-[:FRIEND]-(friend)
WITH n, count(friend) AS friendsCount
WHERE friendsCount > 3
RETURN n, friendsCount
```

其實可以「感覺」的出來就是用「node」和「relationship」來做為查詢的方式，

在每個「node」的上面有屬性，而不同的屬性可以有「條件」來進行過濾。

node 用 () 來標記，關係用 [] 來表示

# 特徵

就自然有人問，那這個資料庫的好處在哪?

大家覺的「圖」這種的設計，比較常出現在哪? 答案就是 `社群` ，它就是點和線的連結而以。

當有這個想法後，例如我好朋友的朋友有哪些?

```
MATCH (i {name: 'me'})-[:FRIEND]-(f1:friend)-[:FRIEND]-(f2:friend)
RETURN n, f1, f2
```

`i` = 我; `f1` = 我朋友; `f2` = 我朋友的朋友

如果想像用 RDBMS，就要用二層 select 來處理(或使用 sub-query)，

但在 graph database 的查詢就只要像上面很簡單的一個查詢即可。

至於效率自然有資料庫的演算法去優化使用，當然用 graph database vs RDBMS 都可以解決問題

但就閱讀上其實圖型資料庫是好理解的，但其實他複雜的都是「資料庫」的設計問題。

這個就可以再開一個單位來討論就是了。