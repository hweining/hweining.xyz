---
layout: post
cid: 26
title: Java Web基礎之Java三大器之一:过滤器
slug: 26
date: 2018/10/14 23:20:00
updated: 2018/11/19 22:08:19
status: publish
author: admin
categories: 
  - 归档
tags: 
---


看知乎一篇回答，说Java如何入门三大框架，

> 学习Servlet和JSP。这个步骤不是可以跳过的，现在流行的框架Spring MVC和Struts2其实都是基于Servlet的，只有深入理解了Servlet才能理解后面的新技术。

Java三大器：拦截器，过滤器，监听器。

# 过滤器定义
1.什么是过滤器？
过滤器是web服务器端的一个组件，可以截获用户的请求和web资源的响应，对请求和响应进行过滤

2.过滤器的工作原理？
拦截用户请求或容器响应，按规则通过或拦截用户的请求。过滤器可以改变用户请求的web资源，也就是说可以改变用户请求的路径，过滤器不能直接返回数据，不能直接处理用户请求，它不是一个标准的servlet。

过滤器不能直接返回数据，不能直接处理用户请求，它不是一个标准的servlet



3.容器的生命周期
- 在web容器启动时依据web.xml实例化 一次
- 初始化 init() 一次
- 过滤 doFilter() 多次
- 销毁 destroy() 一次 web容器关闭

# 如何创建一个过滤器
1.创建一个过滤器类 ，继承自servlet下的Filter
    
        public class FilterDemo1 implements Filter {
            public void destroy() {
            }
    
            public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws ServletException, IOException {
    
                //执行这一句，说明放行（让下一个过滤器执行，如果没有过滤器了，就执行执行目标资源）
                chain.doFilter(req, resp);
            }
    
            public void init(FilterConfig config) throws ServletException {
    
            }
        }

2.重写三个方法init() doFilter() destroy()方法
（1）init()初始化：这个方法可以读取web.xml文件中的过滤器初始化参数。通过参数FilterConfig arg0可以获取更多参数

（2）doFIlter()核心：完成实际的过滤操作。当用户请求访问与过滤器【关联的URL】时，Web容器将先调用过滤器的doFilter方法，FilterChain arg2参数可以调用chain.doFilter方法，将请求传给下一个过滤器（或目标资源），或利用转发，重定向将请求转发给其他资源。

(3)web容器在消耗过滤器前调用该方法，用于释放过滤器占用的资源。（大多数情况用不到）

3.web.xml配置
过滤器的配置可以在设计里面。
过滤器可以改变用户处理的路径，但不能直接处理数。
[![](https://img4.mukewang.com/54ca35000001708f12000530-156-88.jpg)](https://img4.mukewang.com/54ca35000001708f12000530-156-88.jpg)
红色区域代表过滤器类的配置
<filter>...</filter>
绿色区域配置过滤器URL相关映射配置
<filter-mapping>...</filter-mapping>

4.过滤器三方法。
init()、doFilter()、destory()，在过滤器对象的doFilter()方法中，业务逻辑处理完成之后，需要通过FilterChain对象的doFilter(）方法将请求传递到下一个过滤器或者目标资源，否则将出现错误。

过滤器大多数时间消耗在doFilter()方法中,doFilter()方法被Web容器调用,即被服务器调用,因为Web容器存在Tomcat容器中,Tomcat就是服务器.

doFilter()方法同时传入ServletRequest Request、ServletResponse Response和Filter Chain对象的引用.然后过滤器就有机会处理请求,将处理任务传递给链中的下一个资源,(通过调用Filter Chain对象引用上的doFilter方法),之后在处理控制权返回该过滤器时处理响应.

# 过滤器链
a)一个Web应用可以有0个或多个Filter，多个Filter的组合就是过滤器链
b)多个Filter的执行先后顺序，与web.xml文件中配置的顺序有关
c)chain.doFilter(request,response)具有二义性：

    >如果有下一个Filter时，将请求转发给下一个Filter
    >如果无下一个Filter时，将请求转发给Web资源(serlvet/jsp/html)
    d)可以将web资源中的一些公共代码，提取出来，放入Filter中

Web应用允许多个过滤器来过滤页面请求——联想现实生活中的例子是最好理解的啦！比如：为了获得更加干净的水，可能需要多个过滤器来进行过滤。
这个时候就分为两种情况了：

1：多个过滤器过滤的URL不同，那么此时的多个过滤器是互不相干的，各过滤各的，互不干扰

2：多个多虑期过滤的URL相同，那么此时的多个过滤器就形成了一个过滤器链，此时就有个一个问题了Web容器现将对应的请求给谁过滤呢？处理规则也很简单，就是根据在Web.xml文件中配置的声明的顺序来决定，那个先过滤那个在过滤

过滤器链的执行过程：用户请求 -> 依次执行过滤器的放行前方法 -> web资源 -> 依次执行过滤的放行后方法。

[![](https://img4.mukewang.com/59409207000131dc12800720.jpg)](https://img4.mukewang.com/59409207000131dc12800720.jpghttp://)

Tomcat分为container容器，engine容器，Host容器和servlet容器；其中servlet容器中又包括context容器=web工程。
[![](https://img4.mukewang.com/59c0b86a0001966512800720.jpg)](https://img4.mukewang.com/59c0b86a0001966512800720.jpg)

# 过滤器分类
四种类型过滤器: request、forward、include、error
1.使用request过滤器的有：response.sendRedirect()和浏览器直接访问

2.使用forward过滤器的的有：request.getRequestDispatcher().forward(request,response)以及forward动作，目标资源通过RequestDispatcher的forward访问时，该过滤器被调用  <jsp:forward也能触发

3.使用include过滤器的有：request.getRequestDispatcher().include(request,response)以及include动作和指令。目标资源通过RequestDispatcher的include访问时，该过滤器被调用   <jsp:include也能触发    

4.error过滤器 目标资源通过声明式处理机制调用时，该过滤器被调用



# .过滤器的API：
init()、doFilter()、destory()

过滤器配置的另一种方法：
1.Web.xml文件中配置
2.@webFilter(filterName="AsynFilter",value={"/index.jsp"},dispatcherTypes={DispatcherType.REQUEST,DispatcherType.ASYNC})






