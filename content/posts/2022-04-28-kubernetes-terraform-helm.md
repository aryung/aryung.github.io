---
layout: post
title:  "kubernetes terraform helm 起手式"
date: 2022-04-28T18:29:06+08:00
draft: false
tags: 
  - 'kubernetes'
  - 'terraform'
  - 'helm'
---

# 楔子
現在雲端化的產品服務這麼多，怎一開始入門來使用這些服務，這次來聊聊這個話題吧。

# 觀念
主要有幾個東西:
- kubernetes:負責 app 的擴展，穩定服務等等
- helm:負責產生 kubernetes 的設定參數樣版文件
- terraform:負責來設定雲端的資源環境
為了不讓東西討論到細節，就先簡單把 kubernetes 想像成可以使用的程式，而 helm 就想像成去設定程式的一些參數，而 terraform 想像成要用哪些資源。

當然可以去花點時間去官網看看詳細的介紹。

# 環境
為了在本地端使用開發測試，就本地安裝 minikube 來替代 kubernetes ，再簡化說明， helm 就是這些設定 kubernetes 的相關設定，至於 terraform 就當做設定這些程式所需要的相關資源(eg.多大的 cpu 和 ram)

# 示範 code
## 使用 terraform + kubernetes config
terraform 要設定幾個東西
- kubernetes
- mysql deployment & mysql service
- wordpress deployment & wordpress service

```terraform
provider "kubernetes" {
 config_context = "minikube"
}

locals {
 wordpress_labels = {
   App = "wordpress"
   Tier = "frontend"
 }
 mysql_labels = {
   App = "wordpress"
   Tier = "mysql"
 }
}

resource "kubernetes_secret" "mysql-pass" {
 metadata {
   name = "mysql-pass"
 }
 data = {
   password = "root"
 }
}

resource "kubernetes_deployment" "wordpress" {
 metadata {
   name = "wordpress"
   labels = local.wordpress_labels
 }
 spec {
   replicas = 1
   selector {
     match_labels = local.wordpress_labels
   }
   template {
     metadata {
       labels = local.wordpress_labels
     }
     spec {
       container {
         image = "wordpress:4.8-apache"
         name  = "wordpress"
         port {
           container_port = 80
         }
         env {
           name = "WORDPRESS_DB_HOST"
           value = "mysql-service"
         }
         env {
           name = "WORDPRESS_DB_PASSWORD"
           value_from {
             secret_key_ref {
               name = "mysql-pass"
               key = "password"
             }
           }
         }
       }
     }
   }
 }
}

resource "kubernetes_service" "wordpress-service" {
 metadata {
   name = "wordpress-service"
 }
 spec {
   selector = local.wordpress_labels
   port {
     port        = 80
     target_port = 80
     node_port = 32000
   }
   type = "NodePort"
 }
}

resource "kubernetes_deployment" "mysql" {
 metadata {
   name = "mysql"
   labels = local.mysql_labels
 }
 spec {
   replicas = 1
   selector {
     match_labels = local.mysql_labels
   }
   template {
     metadata {
       labels = local.mysql_labels
     }
     spec {
       container {
         image = "mysql:5.6"
         name  = "mysql"
         port {
           container_port = 3306
         }
         env {
           name = "MYSQL_ROOT_PASSWORD"
           value_from {
             secret_key_ref {
               name = "mysql-pass"
               key = "password"
             }
           }
         }
       }
     }
   }
 }
}

resource "kubernetes_service" "mysql-service" {
 metadata {
   name = "mysql-service"
 }
 spec {
   selector = local.mysql_labels
   port {
     port        = 3306
     target_port = 3306
   }
   type = "NodePort"
 }
}
```

## 使用 terraform +  helm
terraform 設定的東西和上面一致，但多了 helm 就可以更簡單了
- terraform config
```terraform
provider "helm" {
 kubernetes {
   config_context = "minikube"
 }
}
resource "helm_release" "mysql" {
 name  = "mysql"
 chart = "${abspath(path.root)}/charts/mysql-chart"
}
resource "helm_release" "wordpress" {
 name  = "wordpress"
 chart = "${abspath(path.root)}/charts/wordpress-chart"
}
```

- helm mysql deployment & helm mysql service & helm values

`helm create mysql-chart`

values
```yaml
replicaCount: 1
image:
 repository: mysql:5.6
 pullPolicy: IfNotPresent
deployment:
 name: mysql-deployment
service:
 name: mysql-service
 type: ClusterIP
 port: 3306
```

deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
 name: {{ .Values.deployment.name }}
 labels:
   {{- include "mysql-chart.labels" . | nindent 4 }}
spec:
 replicas: {{ .Values.replicaCount }}
 selector:
   matchLabels:
     {{- include "mysql-chart.selectorLabels" . | nindent 6 }}
 template:
   metadata:
     labels:
       {{- include "mysql-chart.selectorLabels" . | nindent 8 }}
   spec:
     containers:
       - name: {{ .Chart.Name }}
         image: "{{ .Values.image.repository }}"
         imagePullPolicy: {{ .Values.image.pullPolicy }}
         ports:
           - name: http
             containerPort: {{ .Values.service.port }}
             protocol: TCP
         env:
           - name: MYSQL_ROOT_PASSWORD
             value: 'admin'
```

service
```yaml
apiVersion: v1
kind: Service
metadata:
 name: {{ .Values.service.name }}
 labels:
   {{- include "mysql-chart.labels" . | nindent 4 }}
spec:
 type: {{ .Values.service.type }}
 ports:
   - port: {{ .Values.service.port }}
     targetPort: {{ .Values.service.port }}
     protocol: TCP
     name: http
 selector:
   {{- include "mysql-chart.selectorLabels" . | nindent 4 }}
```
- helm wordpress deployment & helm wordpress service & values

`helm create wordpress-chart`

helm wordpress values
```yaml
replicaCount: 1
image:
 repository: wordpress:4.8-apache
 pullPolicy: IfNotPresent
deployment:
 name: wordpress-deployment
service:
 name: wordpress-service
 type: NodePort
 port: 80
 nodePort: 32000
```

helm wordpress deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
 name: {{ .Values.deployment.name }}
 labels:
   {{- include "wordpress-chart.labels" . | nindent 4 }}
spec:
 replicas: {{ .Values.replicaCount }}
 selector:
   matchLabels:
     {{- include "wordpress-chart.selectorLabels" . | nindent 6 }}
 template:
   metadata:
     labels:
       {{- include "wordpress-chart.selectorLabels" . | nindent 8 }}
   spec:
     containers:
       - name: {{ .Chart.Name }}
         image: {{ .Values.image.repository }}
         imagePullPolicy: {{ .Values.image.pullPolicy }}
         ports:
           - name: http
             containerPort: {{ .Values.service.port }}
             protocol: TCP
         env:
           - name: WORDPRESS_DB_HOST
             value: 'mysql-service'
           - name: WORDPRESS_DB_PASSWORD
             value: 'admin'
```

helm wordpress service
```yaml
apiVersion: v1
kind: Service
metadata:
 name: {{ .Values.service.name }}
 labels:
   {{- include "wordpress-chart.labels" . | nindent 4 }}
spec:
 type: {{ .Values.service.type }}
 ports:
   - port: {{ .Values.service.port }}
     targetPort: {{ .Values.service.port }}
     protocol: TCP
     name: http
     nodePort: {{ .Values.service.nodePort }}
 selector:
   {{- include "wordpress-chart.selectorLabels" . | nindent 4 }}
```

參考資料
- [terraform+kubernetes+helm](https://andreimaksimov.medium.com/the-most-easy-way-to-deploy-apps-to-the-kubernetes-using-terraform-8c79efab734f)