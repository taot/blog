---
title: 配置 Nginx SSL with Self-signed Certificate
date: 2017-02-16 16:23:06
tags:
    - nginx
---

本文转载自 http://www.jb51.net/article/45646.htm

如想更多了解 SSL/TLS, 可以参考 http://seanlook.com/2015/01/07/tls-ssl/

以下步骤创建一个 self-signed certificate, 用于测试环境设置. 生产环境中请使用 CA 中心颁发的证书.

### 使用openssl创建创建服务器私钥，输入相应提示的信息

```
openssl genrsa -des3 -out server.key 1024
```

### 创建证书签名请求（Certificate Signing Request (CSR)）

```
openssl req -new -key server.key -out server.csr
```

### 清除以SSL启动Nginx时提示必须输入密钥

```
cp server.key server.key.org
openssl rsa -in server.key.org -out server.key
```

### 使用刚生成的私钥和CSR进行证书签名

```
openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
```

### 配置 Nginx 密钥

```
listen 443 ssl;
ssl_certificate /usr/local/nginx/conf/server.crt;
ssl_certificate_key /usr/local/nginx/conf/server.key;
```
