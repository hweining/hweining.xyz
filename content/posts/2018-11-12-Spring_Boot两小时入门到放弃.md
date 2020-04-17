---
layout: post
cid: 45
title: Spring Boot两小时入门到放弃
slug: 45
date: 2018/11/12 19:15:00
updated: 2018/11/12 21:18:02
status: publish
author: admin
categories: 
  - 归档
tags: 
---


今天被慕课网的傻逼给气炸了，好好的入门课程居然被举报下架了，只是因为里面有政治不正确原因，改一下例子和代码就可以了还瞎折腾。

# 项目属性配置的各种方法
属性配置
    spring.datasource.url:jdbc:mysql://127.0.0.1:3306/
    spring.datasource.username:root
    spring.datasource.password:123456
    spring.datasource.driver-class-name:com.mysql.jdbc

加载配置属性注解
    @Value
    @Component
    @ConfigurationProperties

# Controller的使用
@Controller的使用
    @Controller：处理http请求
    @RestController：Spring4之后新的注解，原来返回json需要@ResponseBody配合@Controller
    @RequestMapping：配置url映射
	
	

# 数据库操作
数据库操作
`Spring-Data-Jpa -> MySQL`

Spring-Data-Jpa
`JPA(Java Persistence API)定义了一系列对象持久化的标准，目前实现这一规范的产品有Hibernate、TopLink等。`

# 事务管理
1.什么是事务?事务是作为一个逻辑单元执行的一系列操作。它有4个特性 
* 原子性:事务是一个原子操作，由一系列动作组成。事务的原子性确保动作要么全部完成，要么全部失败。 
* 一致性: 一旦事务完成，不管成功还是失败，系统必须确保它所建模的业务处于一致的状态，而不全是部分完成，或者是部分失败，在现实的数据不应有被破坏。 
* 隔离性： 可能有许多事务会同时处理相同的数据， 因此每个事务都应该与其他事务隔离开，防止数据被破坏。 
* 持久性： 一旦事务完成， 无论发生什么，系统发生错误，它的结果都不应该受到影响，这样就能从任何系统崩溃中恢复过来， 通常情况下，事务的记过被写到持久化存储器。

2.我们常用的几个事务： 
* `PROPAGATION_REQUIRED`: 如果存在一个事务，则支持当前的事务，如果没有则开启。 
* `PROPAGATION_SUPPORTS`: 如果存在一个事务，就支持当前事务， 如果没有事务，则以非事务执行。 
* `PROPAGATION_REQUIRES_NEW`: 启动一个新的事务，不依赖当前事务，当前事务挂起。

3.我们模拟一个事务的回滚，体现事务的原子性，第一个save操作不会出现问题，第二个save操作会抛出异常。但是不能部分成功，不能部分失败。这二个操作最终会被回滚。

    @Service
    public class GirlService {
    
        @Autowired
        private GirlRepository girlRepository;
    
        @Transactional
        public void insertTwo() {
            Girl girlA = new Girl("garrett-test", 18, "Z");
            girlRepository.save(girlA);
    
            Girl girlB = new Girl("mayday-test", 21, "BBBBBBBB");
            girlRepository.save(girlB);
        }
    }
    
    @RestController
    public class GirlController {
        @Autowired
        private GirlService girlService;
    
        /**
         * Tests transaction
         */
        @GetMapping(value = "/transaction")
        public void transactionTest() {
            girlService.insertTwo();
        }
    }

4.启动应用，打开Postman，测试API。很显然，操作发生异常进行回滚，数据库未插入任何数据。


