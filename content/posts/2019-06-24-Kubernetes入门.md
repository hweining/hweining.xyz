---
layout: post
cid: 254
title: Kubernetes入门
slug: 254
date: 2019/06/24 15:40:00
updated: 2019/06/27 15:52:31
status: publish
author: admin
categories: 
  - 归档
tags: 
banner: https://codingcompiler.com/wp-content/uploads/2018/01/kubernetes-tutorials.png
contentLang: 0
disableBanner: 0
disableDarkMask: 0
headTitle: 0
TOC: 0
---


# Kubernetes概念
Kubernetes最初源于谷歌内部的Borg，提供了面向应用的容器集群部署和管理系统。
# Kubernetes Dashboard安装
1、下载kubernetes-dashboard.yaml文件
```shell
wget https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml
```

2、修改kubernetes-dashboard.yaml文件
```yaml
# ------------------- Dashboard Deployment ------------------- #

kind: Deployment
apiVersion: apps/v1beta2
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kube-system
spec:
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      k8s-app: kubernetes-dashboard
  template:
    metadata:
      labels:
        k8s-app: kubernetes-dashboard
    spec:
      containers:
      - name: kubernetes-dashboard
        image: registry.cn-hangzhou.aliyuncs.com/kube_containers/kubernetes-dashboard-amd64
        ports:
        - containerPort: 8443
          protocol: TCP
        args:
          - --auto-generate-certificates
```
3、创建kubernetes-dashboard.yaml
```shell
kubectl create -f kubernetes-dashboard.yaml
```
4、查看kubernetes-dashboard容器是否已经运行
```shell
[root@docker-master1 ~]# kubectl get pods -n kube-system
NAME                                     READY   STATUS    RESTARTS   AGE
coredns-576cbf47c7-l5wlh                 1/1     Running   1          3d8h
coredns-576cbf47c7-zrl66                 1/1     Running   1          3d8h
etcd-docker-master1                      1/1     Running   1          3d8h
kube-apiserver-docker-master1            1/1     Running   2          3d8h
kube-controller-manager-docker-master1   1/1     Running   2          3d8h
kube-flannel-ds-amd64-c7wz6              1/1     Running   0          3d8h
kube-flannel-ds-amd64-hqvz9              1/1     Running   0          3d8h
kube-flannel-ds-amd64-w7n4s              1/1     Running   2          3d8h
kube-proxy-8gj2w                         1/1     Running   1          3d8h
kube-proxy-mt6dk                         1/1     Running   0          3d8h
kube-proxy-qtxz7                         1/1     Running   0          3d8h
kube-scheduler-docker-master1            1/1     Running   2          3d8h
kubernetes-dashboard-5f864b6c5f-5s2rw    1/1     Running   0          62m
```