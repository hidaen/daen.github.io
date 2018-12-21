---
layout: post
title: Kubernetes 安装 Consul 群集
category: tech
tags: tech
keywords: kubernetes, consul, docker
description: Kubernetes 上安装 consul 群集, 并同步更新 service
---

## Install

```
$ git clone https://github.com/hashicorp/consul-helm.git
$ cd consul-helm
$ git checkout v0.1.0    // 选择版本
$ helm install --name consul ./

# 执行安装后, 可通过 kubectl get pods 和 kubectl get services 查看安装情况
$ kubectl get pods
NAME                        READY     STATUS             RESTARTS
consul-2fvcm                1/1       Running            0
consul-qp7gj                1/1       Running            0
consul-server-0             1/1       Running            0
consul-server-1             1/1       Running            0
consul-server-2             1/1       Running            0
consul-zvq99                1/1       Running            0

$ kubectl get services
NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)
consul-dns          ClusterIP   172.18.80.14   <none>        53/TCP,53/UDP
consul-server       ClusterIP   None           <none>        8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP

# 安装完成后, 可以 通过 kubectl port-forward consul-server-0 8500:8500, 映射出 ui 查看
```

## 配置 consul 后缀解析

```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ConfigMap
metadata:
  labels:
    addonmanager.kubernetes.io/mode: EnsureExists
  name: kube-dns
  namespace: kube-system
data:
  stubDomains: |
    {"consul": ["$(kubectl get svc consul-dns -o jsonpath='{.spec.clusterIP}')"]}
EOF

# 配置完成后, 可以通过 kubectl describe configmaps --all-namespaces 查看配置

# 验证 consul 安装配置完成
$ kubectl apply -f job.yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: dns
spec:
  template:
    spec:
      containers:
      - name: dns
        image: anubhavmishra/tiny-tools
        command: ["dig",  "consul.service.consul"]
      restartPolicy: Never
  backoffLimit: 4

$ kubectl get pods --show-all | grep dns
dns-lkgzl         0/1       Completed   0          6m

$ kubectl logs dns-lkgzl
; <<>> DiG 9.11.2-P1 <<>> consul.service.consul
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 4489
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 0, ADDITIONAL: 4

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;consul.service.consul.     IN  A

;; ANSWER SECTION:
consul.service.consul.  0   IN  A   10.36.2.23
consul.service.consul.  0   IN  A   10.36.4.12
consul.service.consul.  0   IN  A   10.36.0.11

;; ADDITIONAL SECTION:
consul.service.consul.  0   IN  TXT "consul-network-segment="
consul.service.consul.  0   IN  TXT "consul-network-segment="
consul.service.consul.  0   IN  TXT "consul-network-segment="

;; Query time: 5 msec
;; SERVER: 10.39.240.10#53(10.39.240.10)
;; WHEN: Wed Sep 12 02:12:30 UTC 2018
;; MSG SIZE  rcvd: 206
```

到目前位置, 我们的 consul 已经安装完成, 可以通过 api 或则 cli 等方式注册

## 同步 consul 与 kubenetes service 解析

编辑 consul-helm 项目的 values.yaml 文件, 增加同步配置, 并执行 helm upgrade 更新操作

```
syncCatalog:
  enabled: true
```

> 注意: kubenetes 中的服务, 只有 NodePort, LoadBalancer, External IPs, ClusterIP 等 service 会自动同步到 consul 中


## Test

```
# 在 kubenetes 中部署 service 可自动注册到 consul, consul 中的 service 同样可以注册到 kubenetes 中
```
