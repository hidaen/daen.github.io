---
layout: post
title: Docker 仓库解决方案 Harbor
category: tech
tags: tech
keywords: Docker,Registry,仓库
description: Docker 企业级 Registry 解决方案
---

## 简介

Harbor 是 VMware 公司开源的企业级 Registry 项目, 是由 VMware 中国研发团队负责开发的.

Harbor 是用于存储和分发 Docker 镜像的企业级 Registry 服务器

## install

```
$ wget https://storage.googleapis.com/harbor-releases/release-1.7.0/harbor-offline-installer-v1.7.1.tgz
$ tar zxvf harbor-offline-installer-v1.7.1.tgz
$ cd harbor
# 修改 harbor.cfg
hostname = xxx
ui_url_protocol = https // https 时需配置 ssl_cert, ssl_cert_key
ssl_cert = xxx
ssl_cert_key = xxx
registry_storage_provider_name = filesystem  // 可以配置 filesystem, s3, gcs, azure, etc 等, 具体看官方文档
$ ./install.sh
# 安装完成既可, 默认所有数据在 /data 目录下
```

> 我们这里用等 offline 包, 不用在线拉取 Harbor image, 有一些定制配置可以修改 harbor.cfg, 或则 docker-compose-yml, 有一些配置在 harbor.cfg, docker-compose-yml 没有体现等可以修改安装目录下 common 下的 config 文件.
