---
layout: post
title:  "useContext Provider Consumer"
date: 2022-05-23T18:29:06+08:00
draft: false
tags: 
  - 'react'
  - 'hook'
---

# 楔子
context 就語意上指的就是一個「環境」的概念，就如果轉成電腦來說就是「State」的意思。

而在 react component 有幾種情況可以夾帶這些「環境變數」
- props
- state

React 提供了另一個手法「context」來簡化語法

# The Context API

```
const LocaleContext = React.createContext();
```

LocaleContext 有二個屬性: Provider & Consumer

> Provider allows us to "declare the data that we want available throughout our component tree".

> Consumer allows "any component in the component tree that needs that data to be able to subscribe to it".

# Provider

提供一個 value 的 props

```
<MyContext.Provider value={data}>
  <App />
</MyContext.Provider>
```

比較完整的 code

```
// LocaleContext.js
import React from "react";

const LocaleContext = React.createContext();

export default LocaleContext;

import React from "react";
import LocaleContext from "./LocaleContext";

export default function App() {
  const [locale, setLocale] = React.useState("en");

  return (
    <LocaleContext.Provider value={locale}>
      <Home />
    </LocaleContext.Provider>
  );
}
```

# Consumer

透過 render prop 把值注入 html(jsx)

```
<MyContext.Consumer>
  {(data) => {
    return (
      <h1>The "value" prop passed to "Provider" was {data}</h1>
    );
  }}
</MyContext.Consumer>
```

透過 render prop 把值注入 components

```
// Blog.js
import React from "react";
import LocaleContext from "./LocaleContext";

export default function Blog() {
  return (
    <LocaleContext.Consumer>
      {(locale) => <Posts locale={locale} />}
    </LocaleContext.Consumer>
  );
}
```

# Updating Context State

設定要使用的 set-function

```
export default function App() {
  const [locale, setLocale] = React.useState("en");

  const toggleLocale = () => {
    setLocale((locale) => {
      return locale === "en" ? "es" : "en";
    });
  };

  return (
    <LocaleContext.Provider
      value={{
        locale,
        toggleLocale,
      }}
    >
      <Home />
    </LocaleContext.Provider>
  );
}
```

把 set-function 塞到 value 裡，但這有一個 issue 就是每次的 render 都會產生一個 toogleLocale，要避免這個就使用 useMemo

>To fix this, instead of passing a new object to value every time, we want to give it a reference to an object it already knows about. To do this, we can use the useMemo Hook.

```
export default function App () {
  const [locale, setLocale] = React.useState('en')

  const toggleLocale = () => {
    setLocale((locale) => {
      return locale === 'en' ? 'es' : 'en'
    })
  }

  const value = React.useMemo(() => ({
    locale,
    toggleLocale
  }), [locale])

  return (
    <LocaleContext.Provider value={value}>
      <Home />
    </LocaleContext.Provider>
  )
}
```

這時的 Consumer 就可以 subscribe 

```
// Blog.js
import React from "react";
import LocaleContext from "./LocaleContext";

export default function Blog() {
  return (
    <LocaleContext.Consumer>
      {({ locale, toggleLocale }) => (
        <React.Fragment>
          <Nav toggleLocal={toggleLocale} />
          <Posts locale={locale} />
        </React.Fragment>
      )}
    </LocaleContext.Consumer>
  );
}
```

# defaultValue

可以設定 context 的預設值

```
const MyContext = React.creatContext("defaultValue");
```

```
const LocaleContext = React.createContext("en");
```

context 的值可以覆寫

```
import React from "react";
import ReactDOM from "react-dom";

const ExpletiveContext = React.createContext("shit");

function ContextualExclamation() {
  return (
    <ExpletiveContext.Consumer>
      {(word) => <span>Oh {word}!</span>}
    </ExpletiveContext.Consumer>
  );
}

function VisitGrandmasHouse() {
  return (
    <ExpletiveContext.Provider value="poop">
      <h1>Grandma's House 🏡</h1>
      <ContextualExclamation />
    </ExpletiveContext.Provider>
  );
}

function VisitFriendsHouse() {
  return (
    <React.Fragment>
      <h1>Friend's House 🏚</h1>
      <ContextualExclamation />
    </React.Fragment>
  );
}

function App() {
  return (
    <React.Fragment>
      <VisitFriendsHouse />
      <VisitGrandmasHouse />
    </React.Fragment>
  );
}
```

# useContext

該可以使用 useContext 來示範

```
export default function Nav() {
  return (
    <LocaleContext.Consumer>
      {({ locale, toggleLocale }) =>
        locale === "en" ? (
          <EnglishNav toggleLocale={toggleLocale} />
        ) : (
          <SpanishNav toggleLocale={toggleLocale} />
        )
      }
    </LocaleContext.Consumer>
  );
}
```

如果有多個 context 時就有點巢狀，就會有 performance issue

```
export default function Nav() {
  return (
    <AuthedContext.Consumer>
      {({ authed }) =>
        authed === false ? (
          <Redirect to="/login" />
        ) : (
          <LocaleContext.Consumer>
            {({ locale, toggleLocale }) =>
              locale === "en" ? (
                <EnglishNav toggleLocale={toggleLocale} />
              ) : (
                <SpanishNav toggleLocale={toggleLocale} />
              )
            }
          </LocaleContext.Consumer>
        )
      }
    </AuthedContext.Consumer>
  );
}
```

這時就有 useContext 的方法來使用

```
export default function Nav() {
  const { locale, toggleLocale } = React.useContext(LocaleContext);

  return locale === "en" ? (
    <EnglishNav toggleLocale={toggleLocale} />
  ) : (
    <SpanishNav toggleLocale={toggleLocale} />
  );
}
```

多個 context

```
export default function Nav() {
  const { authed } = React.useContext(AuthedContext);

  const { locale, toggleLocale } = React.useContext(LocaleContext);

  if (authed === false) {
    return <Redirect to="/login" />;
  }

  return locale === "en" ? (
    <EnglishNav toggleLocale={toggleLocale} />
  ) : (
    <SpanishNav toggleLocale={toggleLocale} />
  );
}
```

context 雖然好用但也不要過度使用，畢竟有效能問題

參考資料
- [tyler mcGinnis](https://ui.dev)
