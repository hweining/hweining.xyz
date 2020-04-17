---
layout: post
cid: 10
title: 树莓派初上手|安装Raspbian OS
slug: 10
date: 2018/09/28 17:12:00
updated: 2019/06/10 13:56:07
status: publish
author: admin
categories: 
  - 归档
  - Mac
tags: 
  - 树莓派
  - Raspbian
  - mac
banner: https://www.jameco.com/Jameco/workshop/circuitnotes/raspberry_pi_circuit_note_fig2.jpg
contentLang: 0
disableBanner: 0
disableDarkMask: 0
headTitle: 0
TOC: 0
---


前言
--

> 树莓派（英语：Raspberry
> Pi），是一款基于Linux的单片机计算机。它由英国的树莓派基金会所开发，目的是以低价硬件及自由软件促进学校的基本计算机科学教育。
> 
> 2006年，早期的树莓派的概念来源于Atmel的ATmega644单片机。其原理图和PCB布局可供公众下载。基金会受托人埃·厄普顿（Eben
> Upton）汇集了一批教师学者和计算机爱好者，制作一套启发孩子的计算机[19]。这种计算机是受到了1981年的艾康计算机公司的BBC
> Micro计算机的启发。第一台ARM计算机的原型被装在与一个U盘的大小相同的一个“盒子”中。它一端有一个USB接口，而另一端有一个HDMI接口


树莓派系统安装
-------
去树莓派官网下载最新镜像：[官网地址][1]

Raspbian 有两个版本，左边版本有 DESKTOP， 右边版本没有 DESKTOP。（根据自己需要选择）

将压缩包解压，得到 .img 文件

![树莓派系统][2]

*树莓派3B(或更高版本) ＋ 电源（ Android 手机充电器就行，或者直接插在电脑上） SD 卡(8G+) ＋ 读卡器 USB       鼠标，USB 键盘，HDMI 接口显示器(有的显示器没有 HDMI 接口，需要准备转换头)， HDMI 线, 网线*

刷入工具 Mac上最好用的是 [Etcher][3] 
step1:格式化SD卡

将 SD 卡格式化为 FAT32 格式
![树莓派][4]

step2:使用 Etcher 刻录
使用 Etcher 刻录
![刻录][5]

选择需要刻录的 xxx.img
选择目标 SD 卡
点击 Flash！等待完成
刻录完成后，就可以取出 SD 卡启动了。

启动树莓派
-----
将 SD 卡插到树莓派上，网线接到路由器上，接上显示器，插上电源线
用 SSH 的方式连接树莓派并对其进行操作，在 Raspbian Jessie with Pixel 的最新版系统中已经内置了这个功能，但默认是关闭的，所以需要创建一个名为：" ssh " 的文件；放入到树莓派的根目录中，重新接电开机。
![请输入图片描述][6]

连接上树莓派的电源之后，将网线接入路由器的 Lan 口，另一端连上树莓派，接着打开路由器的管理界面，找到你路由器的 IP 地址。
使用 SSH 命令登陆树莓派：
Raspbian 默认用户名/密码：pi/raspberry

    ssh pi@192.168.xxx.xxx
![ssh登陆][7]

除了 Raspbian OS 还有哪些 OS 可以选择
---------------------------
Raspbian OS 其实并不是官方出品的，它是基于 Debian 的 ARM 定制版本，只不过是受到官方主力推荐的 OS 罢了，还有很多 Linux 的发行版本支持树莓派，下面列出一些：

Raspbian Jessie With PIXEL：树莓派官方推荐系统，基于 Debain 8，带 PIXEL 图形界面。特点是兼容性和性能优秀。

Raspbian Jessie Lite：树莓派官方推荐系统，基于 Debain 8，不带图形界面。特点是兼容性和性能优秀，比 PIXEL 版本的安装包更小。

Ubuntu MATE：Ubuntu MATE 是针对树莓派的版本，界面个性美观。

Snappy Ubuntu Core：Ubuntu 针对物联网（IoT）的一个发行版本。支持树莓派。

CentOS：CentOS 针对 ARM 的发行版。支持树莓派。
Windows IoT：微软官方针对物联网（IoT）的一个 Windows 版本。支持树莓派。

FreeBSD：FreeBSD 针对树莓派的发行版。

Kali：Kali 针对树莓派的发行版，黑客的最爱。

Pidora：在 Fedora Remix 基础上针对树莓派优化过的操作系统。

还有很多，感兴趣可以去这里看看：[树莓派操作系统大全][8]

## 开启VNC服务 ##

在命令行输入：vncserver，然后回车。这只是临时开启的。

命令行输入：sudo raspi-config，然后回车。

选择第七项：“5 Interfacing Options”，回车

选择第三项：“VNC”，回车，

选择是，回车。

最后点选“Finish”完成即可。

VNC客户端推荐使用VNC Viewer。

![请输入图片描述][9]

![请输入图片描述][10]

树莓派换源
-----

首先是大家都知道的 sources.list 源【这里选用了清华大学的源】

$ cat /etc/apt/sources.list
deb https://mirrors.neusoft.edu.cn/raspbian/raspbian/ stretch main non-free contrib
deb-src https://mirrors.neusoft.edu.cn/raspbian/raspbian/ stretch main non-free contrib


参考一下几篇作者文章：
[http://www.ihubin.com/blog/raspberrypi-mac-install-raspbian/][11]
[https://sspai.com/post/37363][12]


  [1]: https://www.raspberrypi.org/downloads/raspbian/
  [2]: http://cloudstorage.ihubin.com/blog/raspberrypi3/raspberrypi3_download_raspbian.png?orig
  [3]: https://etcher.io/
  [4]: http://cloudstorage.ihubin.com/blog/raspberrypi3/raspberrypi3_format_sdcard.jpg?orig
  [5]: http://cloudstorage.ihubin.com/blog/raspberrypi3/raspberrypi3_etcher_burn_image.png?orig
  [6]: https://cdn.sspai.com/attachment/origin/2017/02/07/366853.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1
  [7]: http://cloudstorage.ihubin.com/blog/raspberrypi3/raspberrypi3_login_raspberrypi.png?orig
  [8]: http://wiki.nxez.com/rpi:list-of-oses
  [9]: https://cdn.sspai.com/attachment/origin/2017/02/07/366861.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1
  [10]: https://cdn.sspai.com/attachment/origin/2017/02/07/366866.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1
  [11]: http://www.ihubin.com/blog/raspberrypi-mac-install-raspbian/
  [12]: https://sspai.com/post/37363