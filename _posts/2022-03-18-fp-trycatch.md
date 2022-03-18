---
layout: post
title:  "如何使用 try catch"
published: true
tags: 
  - "javascript"
---

# 楔子
寫程式時噴錯誤是蠻常見的，就每發生一個錯誤，再加一個條件去排除錯誤的問題

往往如此， code 就會開始蠻的有點「巢狀」的感覺
{% highlight javascript %}
if(check) {
  // 1-level
  if(check) {
    // 2-level
    if(check) {
      // 3-level
    } else {
      // 3-level
    }
  } else {
    // 2-level
  }
} else {
  // 1-level
}
{% endhighlight %}

# 原來的 try-catch
標準的 try-catch 程式長相大概如下， try-catch 主要的功用就是避免程式噴錯跳離

{% highlight javascript %}
try {
  // do something
} catch (e) {
  console.log(e.message)
}
{% endhighlight %}

但也常常有個問題就是錯誤的類型如果不確定，就常常一直補 code 除錯。

所以常常的流程大概就是

包一個 try-catch ，噴錯看 log ，補 if-else 的 bug handling，再重覆 try-catch 的 log..

{% highlight javascript %}
// version 1
function go(x) {
  try {
    let len = x.length
    return [...len, 1]
  } catch (e) {
    console.log(e.message)
  }
}

// version 2
function go1(x) {
  try {
    if (x !== undefined) {
      let len = x.length
      return [...len, 1]
    }
  } catch (e) {
    console.log(e.message)
  }
}

go(undefined) // 噴 undefined 沒有 length
go(['hello'])
{% endhighlight %}

那是不是有方式可以反正噴就直接先給 null (駝鳥心態..嘿嘿..)

這種情況蠻常發生的，就是當取得的資料是從外部或資料庫來的，

有時存的東西和想像的不太一致(應該說東西愈來愈多總會有些不受控的情況)

有沒有先可以把流程跑完再回來慢慢解..

# 另一種的 tryCatch
這是換一個角度想，就我們用一個 function 然後執行它，

如果有任何的噴錯，接下來進行所有的動作都「忽略不處理」，

沒錯誤就一直走下去，到時看結果有些不太合理的結果再回來檢查數據來源，

至少程式處理端是沒有問題的，也比較好了解真正的錯誤原因(數據方面)

來試試看吧

先運用之前提的 Box 的觀念，把值塞在裡面..

{% highlight javascript %}
function tryCatch(f) {
  try {
    return Right(f())
  } catch (e) {
    return Left(e)
  }
}

let go1 = tryCatch(() => undefined.length).map(x => x + 1) // Left(e)
let go2 = tryCatch(() => 2).map(x => x + 1) // Right(2)

{% endhighlight %}

# 參考 code
{% highlight javascript %}
const Box = x =>
({
  map: f => Box(f(x)),
  inspect: () => `Box(${x})`
})

const Right = x =>
 ({
   map : f => Right(f(x)),
   inspect : () => `Right(${x})`
 })

const Left = x =>
  ({
    map : f => Left(x),
    inspect : () => `Left(${x})`
  })
{% endhighlight %}