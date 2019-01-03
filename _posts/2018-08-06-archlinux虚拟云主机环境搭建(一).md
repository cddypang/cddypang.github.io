---
layout:     post
title:      archlinux虚拟云主机环境搭建(一)
subtitle:   --archlinux-xfce4-lightdm-xrdp
date:       2018-08-06
author:     PGJ
header-img: img/post-bg-2015.jpg
catalog: false
tags:
    - Linux
---

## 0. 基础环境

配置环境为网络云主机，系统创建后网络参数已配置完成，可以ssh登录。

\#pacman -Syy

同步软件源缓存数据库


## 1. 安装 xfce4

\#pacman -Sy xfce4 xfce4-goodies

执行默认安装即可


## 2. 安装 lightdm

\#pacman -Sy lightdm

lightdm (Light Display Manager) 是一款轻量级的图形界面管理工具。

安装后修改配置文件：

\#vim /etc/lightdm/lightdm.conf

[Seat:*]  
greeter-session=lightdm-gtk-greeter

设置lightdm.service开机启动  
\#systemctl enable lightdm.service

lightdm.service启停
<br/>\#systemctl start lightdm.service
<br/>\#systemctl stop lightdm.service

**执行到此处重启系统即可以进入图形启动界面，输入用户名密码可正常进入系统。**


## 3. 远程登录图形界面

### 3.1 vnc

  可使用tigervnc进行vnc配置

### 3.2 xrdp

  xrdp方式未配置成功，后续再研究。

**ArchLinux虚拟云主机暂时先折腾到此处，后续再继续添加web环境。**

