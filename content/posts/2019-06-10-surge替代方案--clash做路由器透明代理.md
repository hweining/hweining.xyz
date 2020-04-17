---
layout: post
cid: 220
title: surge替代方案--clash做路由器透明代理
slug: 220
date: 2019/06/10 16:39:00
updated: 2019/06/17 11:54:17
status: hidden
author: admin
categories: 
  - 归档
  - Mac
tags: 
banner: https://img10.360buyimg.com/img/jfs/t1/31123/23/13142/16501/5cb9b83cE4d9065ba/c7fd935e5357800f.png
contentLang: 0
disableBanner: 0
disableDarkMask: 0
headTitle: 0
TOC: 0
---


![](https://img10.360buyimg.com/img/jfs/t1/31123/23/13142/16501/5cb9b83cE4d9065ba/c7fd935e5357800f.png)
Clash 是一个使用 Go 开发的、基于规则的多平台代理客户端，支持诸多协议，拥有像 Surge 一样强大的代理规则，现在已经有了在 Windows、macOS、Android 上的客户端。KoolClash 是运行在 Koolshare OpenWrt/LEDE 的 Clash 客户端。

优点：
支持多种协议： http/Vmess/$$/Socks5 （不支持$$r）
支持域名/IP 规则分流 ，策略分流 （像iOS上面的app一样的分流）
支持服务器均衡负载，测速选最快的服务器
简单来说就是：
aaa.com 设置走 $$的代理
bbb.com 设置走Vmess的代理
ccc.com 设置走HTTP/HTTPS的代理
通俗点说就是，我可以设置策略：
YouTube 走流量多一个$$1
twitter 走速度快的Vmess
Facebook 走另一个$$2
支持全平台，因为是GO开发的，目前开发者就编译好了多个平台的二进制文件

缺点：
体积比较大，因为是GO开发的
还不支持udp


路由器上的简单使用：
1、需要一份可用的配置文件，可以从下面的地址获取：

https://github.com/Hackl0us/SS-R ... AZY_RULES/clash.yml

根据自己的服务器配置好，http/Vmess/$$/Socks5只要你有的都可以设置进去。
注意修改DNS部分：
dns:
  enable: true
  ipv6: false
  listen: '0.0.0.0:5858'
  enhanced-mode: redir-host
  nameserver:
    - 127.0.0.1:5053
复制代码
因为clash自带的DNS功能有时候并不能解决DNS污染，所以我用了L大的代码编译dnsforwarder，当然你也可以使用smartdns


2、从项目地址里下载自己路由器可运行的二进制文件：
https://github.com/Dreamacro/clash/releases

3、将配置文件和可执行文件用WinSCP上传到路由器 /etc/clash上，给clash运行权限，  之后就可以运行了   ./clash -d /etc/clash ， 会提示下载Country.mmdb ，有3M大小（一来一去就占了10m，空间不足的自己想办法了）
     出现以下信息说明运行成功：

    root@OpenWrt:/etc/clash# ./clash -d /etc/clash
    INFO[0000] DNS server listening at: 0.0.0.0:5858        
    INFO[0000] Redir proxy listening at: :7892              
    INFO[0000] HTTP proxy listening at: :7890               
    INFO[0000] SOCKS proxy listening at: :7891              
    INFO[0000] RESTful API listening at: 0.0.0.0:9090



4、设置透明代理
  将下面的脚本保存到/etc/init.d/，命名为clash，给运行权限

    #!/bin/sh /etc/rc.common
    # Copyright (C) 2009-2010 OpenWrt.org
    
    START=99
    STOP=15
    
    SERVICE_USE_PID=1
    
    
    CLASH="/etc/clash/clash"
    CLASH_CONFIG="/etc/clash"
    DNSSERVER="127.0.0.1#5858"
    start() {
            # 启动 Clash
            $CLASH -d "$CLASH_CONFIG" > /dev/null 2>&1 &
            
            sleep 2
            
            # 设置 iptables
            iptables -t nat -N CLASH
            
            # 8080 是 ss 代理服务器的端口，即远程 CLASH 服务器提供服务的端口，如果你有多个 ip 可用,但端口一致，就设置这个
            iptables -t nat -A CLASH -p tcp --dport 8080 -j RETURN
            
            # 192.192.192.192 是 CLASH 代理服务器的 ip, 如果你只有一个 CLASH服务器的 ip，却能选择不同端口,就设置此条
            iptables -t nat -A CLASH -d 192.192.192.192 -j RETURN
            
            # 保留地址、私有地址、回环地址 不走代理
            iptables -t nat -A CLASH -d 0.0.0.0/8 -j RETURN
            iptables -t nat -A CLASH -d 10.0.0.0/8 -j RETURN
            iptables -t nat -A CLASH -d 127.0.0.0/8 -j RETURN
            iptables -t nat -A CLASH -d 169.254.0.0/16 -j RETURN
            iptables -t nat -A CLASH -d 172.16.0.0/12 -j RETURN
            iptables -t nat -A CLASH -d 192.168.0.0/16 -j RETURN
            iptables -t nat -A CLASH -d 224.0.0.0/4 -j RETURN
            iptables -t nat -A CLASH -d 240.0.0.0/4 -j RETURN
            
            # 7892是clash_redir端口
            iptables -t nat -A CLASH -p tcp -j REDIRECT --to-ports 7892
            
            iptables -t nat -A PREROUTING -p tcp -j CLASH
                    
            sleep 2
            
            #修改dnsmasq
            uci delete dhcp.@dnsmasq[0].server
            uci add_list dhcp.@dnsmasq[0].server=$DNSSERVER
            uci delete dhcp.@dnsmasq[0].resolvfile
            uci set dhcp.@dnsmasq[0].noresolv=1
            uci commit dhcp
            /etc/init.d/dnsmasq restart > /dev/null 2>&1 &
    }
    
    stop() {
            # 清除 iptables
        iptables -t nat -D PREROUTING -p tcp -j CLASH
            iptables -t nat -F CLASH
            iptables -t nat -X CLASH
            
            #还原dnsmasq修改
            uci delete dhcp.@dnsmasq[0].server
            uci delete dhcp.@dnsmasq[0].resolvfile
            uci delete dhcp.@dnsmasq[0].noresolv
            uci commit dhcp
            /etc/init.d/dnsmasq restart > /dev/null 2>&1 &
            
            sleep 1
            # 关闭 Clash
            kill -9 `pidof clash|sed "s/$//g"` 2>/dev/null
            
    }
    
    
    /etc/init.d/clash enable    # 开机启动clash  
    
    /etc/init.d/clash start     # 启动clash
    
    /etc/init.d/clash stop      # 停止clash
    

运行成功后有一个服务界面，访问   http://clash.razord.top  填入RESTful API 的监控端口：

