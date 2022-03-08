---
layout: post
title:  "如何在 useEffect 內使用 async function"
published: true
tags: 
  - "javascript"
  - "hook"
---

# 使用情境
- 一開始的 API 呼叫把值塞進 React
- 搜尋 autocomplete

前端一開始開啟都會有呼叫 API 情境的狀況發生，比較基本的方式使用大概會長這樣子。

{% highlight jsx %}
const Users = () => {
  const [users, setUsers] = useState([])

  useEffect(() => {
    fetchUsers().then((users) => setUsers(users))
  }, [])

  if (!users) return <div>Loading...</div>

  return (
    <ul>
      {users.map((user) => (
        <li>{user.name}</li>
      ))}
    </ul>
  )
}
{% endhighlight %}

那如果要使用 async/await 呢?

很直覺的會這樣寫

{% highlight jsx %}
// 直接在 useEffect 內使用 async
useEffect(async () => {
  const users = await fetchUsers()
  setUsers(users)
}, [])
{% endhighlight %}

但會有一個情況發生，還記的 useEffect 有一個 cleanup function 嗎?

用上面的寫法會有沒法執行 cleanup function 的情況發生

{% highlight javascript %}
// 直接在 useEffect 內使用 async
useEffect(async () => {
  const users = await fetchUsers()
  setUsers(users)

  return () => {
    // this never gets called, hello memory leaks...
  }
}, [])
{% endhighlight %}

why ? 因為當執行 setUsers 時，就又直接重新 render 了..

比較好的方式就是大概有二種

- 直接使用 IIFE

{% highlight javascript %}
useEffect(() => {
  (async () => {
    const users = await fetchUsers()
    setUsers(users)
  })()

  return () => {
    // this now gets called when the component unmounts
  }
}, [])
{% endhighlight %}

- 在 useEffect 內寫一個 async function

{% highlight javascript %}
useEffect(() => {
  const getUsers = async () => {
    const users = await fetchUsers()
    setUsers(users)
  }

  getUsers()

  return () => {
    // this now gets called when the component unmounts
  }
}, [])
{% endhighlight %}