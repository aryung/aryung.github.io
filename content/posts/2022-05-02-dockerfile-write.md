---
layout: post
title:  "Dockerfile 小練習"
date: 2022-05-02T18:29:06+08:00
draft: false
tags: 
  - "docker"
  - "devops"
---
# 楔子
Docker 是現在開發的基本工，除了不想污染本地的環境以外，更重要的是可以把執行環境標準化，不會因為不同的環境就有無法執行的狀況，來試著了解一下吧。

網路的 docker 說明介紹蠻多的，就不多做說明了，就比較用講解的方式來自已可以寫一個所屬的 dockerfile 的實做羅輯。

# 環境架構
自行參考相關安裝
- MacOS
- linux
- windows

# 認識 images 與 tag
來試看看 ubuntu 的 base image 來看， `(image name):(tag)` 這是基本的格式，這個格式可以使用在 dockerfile 內。
[ubuntu example](https://hub.docker.com/_/ubuntu?tab=tags)

# 試題來試試
## case I
Hint: (為 dockerfile 指令)
- 基礎的影像 base image (FROM)
- 每個程式都會歷經 build test(optional) runtime 的過程 (RUN CMD)
- 把原來的程式碼 copy 到 docker 內的 workdir (COPY WORKDIR)
- 安裝相關程式(dependency) (RUN)

- 試著寫出一個 NodeApp Dockerfile
提示: 下方為 build & runtime 的 bash 指令
```javascript
npm run build
npm run start
```

dockerfile 步驟

```dockerfile
# step 1: image source

# step 2: set workdir

# step 3: app install

# step 4: extra package if needed

EXPOSE 3000

# step 5: cmd
CMD ["npm", "run", "start"]
```

來試著提供下面的答案

```dockerfile
# step 1: image source
FROM node:10.18.0-alpine3.11
# step 2: set workdir
WORKDIR /work
# step 3: app install
COPY . /work
# step 4: extra package if needed
RUN npm install && npm run build
EXPOSE 3000
# step 5: cmd
CMD ["npm", "run", "start"]
```

參考文獻
- [Docker install](https://www.docker.com/get-started/)
- [DockerHub](https://hub.docker.com/)