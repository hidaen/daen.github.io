---
layout: post
title: Docker 常用脚本
category: tech
tags: tech
keywords: docker,script,脚本
description: Docker 常用脚本
comments: true
---

### Docker clean

清除已经退出的 container 和, cache 的 images

```
#!/bin/bash
set -e

echo "Removing containers ..."
docker ps -a | grep 'Exited' |awk '{print $1}'| xargs docker rm

echo "Removing images ..."
docker images | grep '<none>' |awk '{print $3}'| xargs docker rmi
```

### docker ip

获取 container 使用的 ip

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

import os
import subprocess
from argparse import ArgumentParser


def call(cmd):
    retcode = subprocess.call(cmd, shell=True)
    if retcode:
        raise Exception('Failed to execute command "%s".' % cmd)


parser = ArgumentParser()
parser.add_argument('name', nargs='?', help='contain id or name')
args = parser.parse_args()
contain = args.name
if contain:
    cmd = "docker inspect --format '{{.Name}} - {{.NetworkSettings.IPAddress}}' " + contain
else:
    cmd = "docker inspect -f '{{.Name}} - {{.NetworkSettings.IPAddress }}' $(docker ps -aq)"
call(cmd)
```
