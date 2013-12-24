---
title: 数据挖掘工作环境搭建
layout: post
guid: urn:uuid:b87da13a-a4dd-402f-b06a-cef7eeee2d86
tags:
  - 数据挖掘
  - 高性能计算
---

##前情提要

最近把自己家电脑放公司来了，自带干粮五毛党，发现干活顺利了很多，腰不酸背不疼了。

##准备工作

硬件：主机+一块支持CUDA的主流N卡

系统：UBUNTU 12.04 LTS

软件：（按顺序）

+移除OpenJDK后安装JAVA 6u43 64bit（不是最新）
+CUDA Toolkit 5.5，配置完后要声明无数变量
+R 3.0.2 tar 包直接编译安装，配置完后要声明JAVA_HOME变量
+R Studio Server，依赖前两个可以使用远程登录R（http://[ipaddress]:8787）
+SSH登陆
+Anaconda Python 1.8.0，安装后可以直接用Conda或Pip管理Python包
+MediaWiki 1.22，要先配置MySQL和Apache PHP，会顺便配好FTP