---
layout: post
title: consul 简介
category: tech
tags: tech
keywords: consul
description: consul 简介
comments: true
---

> Consul 是 HashiCorp 公司推出的开源工具，用于实现分布式系统的服务发现与配置. 其使用 go 语言开发, 提供 web 界面, 跨平台支持等.

## 安装

```
从 www.consul.io/downloads.html 下载二进制文件到本地即可.
consul 在每个节点上都只需要相同的单一二进制文件, 部署十分简单.
```

## 基本概念

### agent

组成 consul 集群的每个成员上都要运行一个 agent, 可以通过 consul agent 命令来启动, agent 可以运行在 server 或 client 状态. 运行在 server 状态状态的节点叫 server 节点, 运行在 client 节点的叫 client 节点.

### client

负责转发所有 RPC 到 server 节点, 本身无状态, 非常轻量级, 因此可大量部署 client 节点.

### server

负责 cluster 的复杂工作(选举, 状态维护, 转发请求等), 一般会部署基数个(3,5...)

## 使用

```
# 单机运行测试(不建议线上使用, 我们这里启动一个测试)
$ consul agent -dev

# 启动后在前台运行, 启动一个新窗口可以执行相关命令或则通过 api 查看

# 查看成员
$ consul members

# api
$ curl 127.0.0.1:8500/v1/catalog/nodes

# dig
$ dig  @127.0.0.1 -p 8600 consul.service.consul

```
