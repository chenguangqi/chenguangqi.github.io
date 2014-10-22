---
layout: articles_item
title: 配置HTTP二级代理
author: chenguangqi
categories: [Tools]
---

## 安装squid

使用squid配置HTTP二级代理

```bash
sudo apt-get install squid
```

## 配置squid

```squid
http_access deny all
http_port 3128
cache_peer 72.218.251.120       parent    8080  0  default no-query
```

上面的`72.218.251.120`与`8080`需要替换为正确代理的IP地址和端口号。

## 重启squid服务

```bash
sudo service squid3 restart
```

## 测试

```bash
curl --proxy 127.0.0.1:3128 http://www.baidu.com -v
```
出现下面的信息，说明配置成功！
>
>< HTTP/1.0 200 OK
>
