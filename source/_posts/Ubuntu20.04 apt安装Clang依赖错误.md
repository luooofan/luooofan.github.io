---
title: Ubuntu20.04 apt安装Clang依赖错误
date: 2021-09-20 19:24:20
categories: 记录
tags: [记录, Server, Linux]
description: Ubuntu20.04使用apt安装Clang遇到依赖错误的解决方案
keywords:
cover: false
---

# Ubuntu20.04 apt安装Clang依赖错误

## Problem

执行`sudo apt-get install clang-10`报如下错误：

`clang-10 : Depends: libclang-common-10-dev (= 1:10.0.1~++20210822...) but it is not going to be installed`

如图：

![image-20210920193348403](http://img.luooofan.site/20210920-200020-image-20210920193348403.png)



## Solution

照着它输出的无法安装的依赖不断apt install：

![image-20210920193408961](http://img.luooofan.site/20210920-200022-image-20210920193408961.png)

![image-20210920193435372](http://img.luooofan.site/20210920-200022-image-20210920193435372.png)

这样多次尝试之后，会发现以下这两个包是冲突的：都需要9.2但是已经安装了9.3

`sudo apt install libc6=2.31-0ubuntu9.2 libc-bin=2.31-0ubuntu9.2`

其实应该还有相关包也冲突了，可运行如下命令：

`sudo apt autoremove`

或者就像上面一样不断apt install来找到冲突的包然后安装所需要的版本

然后就可以安装clang了：

`sudo apt-get install clang-10`

![image-20210920194328469](http://img.luooofan.site/20210920-200023-image-20210920194328469.png)



## Addition

一般apt install不成功先更新一下然后重新install：`sudo apt-get update`

有时候需要换源或者补充源：`sudo vim /etc/apt/sources.list`

可以用apt搜索要安装的包：`sudo apt search clang`

可以用apt查找已经安装的包：`sudo apt list --installed | grep llvm`



## Reference

- [llvm apt sources](https://github.com/actions/virtual-environments/issues/104#issuecomment-555806825)

- [Unmet dependencies. Libc6 The package system is broken](https://askubuntu.com/questions/1315906/unmet-dependencies-libc6-the-package-system-is-broken)
