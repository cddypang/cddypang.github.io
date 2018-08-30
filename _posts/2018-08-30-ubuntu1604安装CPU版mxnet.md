---
layout:     post
title:      ubuntu1604安装CPU版mxnet
subtitle:    
date:       2018-08-30
author:     PGJ
header-img: img/post-bg-2015.jpg
catalog: false
tags:
    - DL
--- 

### 0. 准备

ubuntu16.04 自带python2.7 python3.5 两个版本，默认使用python2。

$sudo apt install build-essential libatlas-base-dev libopencv-dev git

切换到工程目录
git clone --recursive https://github.com/dmlc/mxnet.git

进入到你下载的那个文件进行：make

上一步如果：

git clone https://github.com/dmlc/mxnet.git

会报错：

Makefile:74: /home/abc/mxnet/3rdparty/mshadow/make/mshadow.mk: 没有那个文件或目录
Makefile:75: /home/abc/mxnet/3rdparty/dmlc-core/make/dmlc.mk: 没有那个文件或目录
Makefile:168: "USE_LAPACK disabled because libraries were not found"
Makefile:276: WARNING: Significant performance increases can be achieved by installing and enabling gperftools or jemalloc development packages
Makefile:347: /home/abc/mxnet/3rdparty/ps-lite/make/ps.mk: 没有那个文件或目录
make: *** 没有规则可制作目标“/home/abc/mxnet/3rdparty/ps-lite/make/ps.mk”。 停止。




