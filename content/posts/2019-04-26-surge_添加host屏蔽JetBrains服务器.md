---
layout: post
cid: 60
title: surge 添加host屏蔽JetBrains服务器
slug: 60
date: 2019/04/26 11:35:00
updated: 2019/06/10 11:28:31
status: publish
author: admin
categories: 
  - 归档
  - Mac
tags: 
  - surge
  - jetbrains
banner: https://nssurge.com/img/screenshot-dashboard.png
contentLang: 0
disableBanner: 0
disableDarkMask: 0
headTitle: 0
TOC: 0
---


![](https://nssurge.com/img/screenshot-dashboard.png)
surge这个配置不仅包含了本机的/etc/hosts功能，而且还提供了 泛解析 和 别名 支持，相当好用。
举个栗子：
[![](https://hweining.online/usr/uploads/2019/04/4206559772.png)](https://hweining.online/usr/uploads/2019/04/4206559772.png)

将“0.0.0.0 account.jetbrains.com”及“0.0.0.0 www.jetbrains.com”添加到hosts文件中

在macOS就不和windows一样，需要改成127.0.0.1

0.0.0.0一般是接纳与路由表不配对的其他ip

比如ss配置文件写0.0.0.0，表示任何ip都能与之连接。

在surge规则[host]里面添加两条规则就行：

    account.jetbrains.com = 127.0.0.1
    www.jetbrains.com = 127.0.0.1