---
layout: post
title: Kubenetes 简介
category: tech
tags: tech
keywords: docker, kubernetes, introduction
description: kubernetes 简介
comments: true
---

> Kubenetes 简介, 可参考官方文档 [what is kubernetes](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/)

Kubernetes 是谷歌开源的容器集群管理系统, 是 Google 多年大规模容器管理技术 Borg 的开源版本, 主要功能包括:

- 基于容器的应用部署, 维护和滚动升级
- 负载均衡和服务发现
- 跨机器和跨地区的集群调度
- 自动伸缩
- 无状态服务和有状态服务
- 广泛的 Volume 支持
- 插件机制保证扩展性

Kubernetes 发展非常迅速, 已经成为容器编排领域的领导者.

## 核心组件

Kubernetes 主要由以下几个核心组件组成:

- etcd 保存了整个集群的状态
- apiserver 提供了资源操作的唯一入口, 并提供认证, 授权, 访问控制, API 注册和发现等机制
- controller manager 负责维护集群的状态, 比如故障检测, 自动扩展, 滚动更新等
- scheduler 负责资源的调度, 按照预定的调度策略将 Pod 调度到相应的机器上
- kubelet 负责维护容器的生命周期, 同时也负责 Volume (CVI)和网络 (CNI) 的管理
- Container runtime 负责镜像管理以及 Pod 和容器的真正运行 (CRI)
- kube-proxy 负责为 Service 提供 cluster 内部的服务发现和负载均衡

## 基本概念

### Container

既然使用 Kubenetes 相信对 container 都有一定对熟悉度, 具体可参考 [Docker](https://docs.docker.com/) 相关文档

### Pod

Kubernetes 使用 Pod 来管理 Container, 每个 Pod 可以包含一个或多个紧密关联的 Container.

Pod 是一组紧密关联的 Container 容器集合, 它们共享 PID, IPC, Network 和 UTS namespace; 是 Kubernetes 调度的基本单位. Pod 内的多个容器共享网络和文件系统, 可以通过进程间通信和文件共享这种简单高效的方式组合完成服务.

### Node

Node 是 Pod 真正运行的主机, 可以是物理机, 也可以是虚拟机. 为了管理 Pod, 每个 Node 节点上至少要运行 Container runtime (比如 docker 或者 rkt), kubelet 和 kube-proxy 服务

### Namespace

Namespace 是对一组资源和对象的抽象集合, 比如可以用来将系统内部的对象划分为不同的项目组或用户组, 常见的 pods, services, replication controllers 和 deployments 等都是属于某一个 namespace 的 (默认是 default), 而 node, persistentVolumes 等则不属于任何 namespace

### Service

Service 是应用服务的抽象, 通过 labels 为应用提供负载均衡和服务发现. 匹配 labels 的 Pod IP 和端口列表组成 endpoints, 由 kube-proxy 负责将服务 IP 负载均衡到这些 endpoints 上.
每个 Service 都会自动分配一个 cluster IP (仅在集群内部可访问的虚拟地址) 和 DNS 名, 其他容器可以通过该地址或 DNS 来访问服务, 而不需要了解后端容器的运行.

### Label

Label 是识别 Kubernetes 对象的标签, 以 key/value 的方式附加到对象上 (key 最长不能超过 63 字节, value 可以为空, 也可以是不超过 253 字节的字符串).

Label 不提供唯一性, 并且实际上经常是很多对象 (如 Pods) 都使用相同的 label 来标志具体的应用.

Label 定义好后其他对象可以使用 Label Selector 来选择一组相同 label 的对象 (比如 ReplicaSet 和 Service 用 label 来选择一组 Pod). Label Selector 支持以下几种方式:

- 等式, 如 app=nginx 和 env!=production
- 集合, 如 env in (production, qa)
- 多个 label (它们之间是 AND 关系), 如 app=nginx,env=test
