---
layout: post
title: shadowsocks server 搭建
category: tech
tags: tech
keywords: shadowsocks,翻墙,GFW
description: shadowssocks server 搭建
---

> 众所周知的原因, 就直接进入搭建环节哈.

### ubuntu:

```
apt-get install shadowsocks

cat /etc/shadowsocks/config.json
{
    "server":"提供服务器的ip"
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"密码",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": true,
    "workers": 10,
    "prefer_ipv6":false
}

启动:
ssserver -c /etc/shadowsocks/config.json -d start

直接写入 rc.local 开机自启动
```

### client

[shadowssocks client 地址](https://shadowsocks.org/en/download/clients.html)


![shadowssocks client](/assets/img/tech/shadowsocks-client.png)


