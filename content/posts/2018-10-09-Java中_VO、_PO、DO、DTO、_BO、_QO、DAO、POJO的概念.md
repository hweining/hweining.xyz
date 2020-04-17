---
layout: post
cid: 24
title: Java中 VO、 PO、DO、DTO、 BO、 QO、DAO、POJO的概念
slug: 24
date: 2018/10/09 21:30:00
updated: 2019/04/28 09:14:11
status: publish
author: admin
categories: 
  - 归档
tags: 
  - Java
---


# 概念：
VO（View Object）：视图对象，用于展示层，它的作用是把某个指定页面（或组件）的所有数据封装起来。

DTO（Data Transfer Object）：数据传输对象，这个概念来源于J2EE的设计模式，原来的目的是为了EJB的分布式应用提供粗粒度的数据实体，以减少分布式调用的次数，从而提高分布式调用的性能和降低网络负载，但在这里，我泛指用于展示层与服务层之间的数据传输对象。

DO（Domain Object）：领域对象，就是从现实世界中抽象出来的有形或无形的业务实体。

PO（Persistent Object）：持久化对象，它跟持久层（通常是关系型数据库）的数据结构形成一一对应的映射关系，如果持久层是关系型数据库，那么，数据表中的每个字段（或若干个）就对应PO的一个（或若干个）属性。

 DAO(data access object) 数据访问对象，是一个 sun 的一个标准 j2ee 设计模式， 这个模式中有个接口就是 DAO ，它负持久层的操作。为业务层提供接口。此对象用于访问数据库。通常和 PO 结合使用， DAO 中包含了各种数据库的操作方法。通过它的方法 , 结合 PO 对数据库进行相关的操作。夹在业务逻辑与数据库资源中间。配合 VO, 提供数据库的 CRUD 操作。
 
POJO，"Plain Ordinary Java Object"，简单普通的java对象。主要用来指代 那些没有遵循特定的java对象模型，约定或者框架的对象。即简单的无规则java对象。

POJO的内在含义是指那些:
有一些private的参数作为对象的属性，然后针对每一个参数定义get和set方法访问的接口。
没有从任何类继承、也没有实现任何接口，更没有被其它框架侵入的java对象。

最简单的实体类是POJO类，含有属性及属性对应的set和get方法，实体类常见的方法还有用于输出自身数据的toString方法。

