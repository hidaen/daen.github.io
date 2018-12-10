---
layout: post
title: expect ssh jumpserver
category: tech
tags: tech
keywords: expect,ssh,jumpserver,script,脚本
description: expect ssh jumpserver
---

在工作我们会经常遇到登陆跳板机的烦恼, 本文通过 expect 实现自动登陆跳板机, 以及通过跳板代理登陆目标机器

mac os 用户安装 expect:

```
brew install expect
```

### 登陆跳板机

```
$ cat ssh-jumpserver.sh
#!/usr/bin/expect -f

set user "USERNAME"
set jumpserver "JUMPSERVER"
set password "PASSWORD"
spawn ssh ${user}@${jumpserver}
expect "?assword:"
send "${password}\r"
interact

user: 登陆跳板机用户
password: 用户密码
jumpserver: 跳板机地址

ssh-jumpserver.sh
```

### 通过跳板机登陆目标机

```
$ cat ssh-proxy.sh
#!/usr/bin/expect -f

set user "USERNAME"
set jumpserver "JUMPSERVER"
set password "PASSWORD"
spawn ssh -o "ProxyCommand ssh ${user}@${jumpserver} -q -W %h:%p" ${argv}
expect "?assword:"
send "${password}\r"
interact

user: 登陆跳板机用户
password: 用户密码
jumpserver: 跳板机地址

登陆目标机 1.1.1.1 用户为root
./ssh-proxy.sh root@1.1.1.1 即可直接登陆
```


