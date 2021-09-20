---
title: matplotlib导致nohup失败
date: 2021-03-29 08:34:33
categories: Server
tags: [Linux, Server]
description: matplotlib导致nohup失败的问题及解决办法
keywords: 
cover: false
---
# matplotlib导致nohup失败

## Linux后台运行命令
一些基本的操作参见[linux 下后台运行python脚本](https://www.jianshu.com/p/4041c4e6e1b0)
主要就是命令后加 **&** ,以及一个`nuhup`命令
此外，还有`jobs, bg, fg, kill`这些相关命令以及快捷键`ctrl-z`
这里不再赘述

## 问题
使用Xshell ssh连接服务器，nohup+&运行一个python脚本，脚本里使用了matplotlib，如果关闭Xshell的话也会把之前运行的python进程杀掉

## 具体表现
查阅资料后，有的说要在执行`nohup`命令后，主动使用`exit`命令退出终端才可以，可以解决一部分情况，但不适用于我这次遇到的问题

具体表现是，Xshell使用`exit`退出后，正常情况下，还会输出一些信息。

但是我使用exit命令之后只显示了logout，没有显示后面的内容

同时打开另一个终端来查看的话，发现此时python脚本仍在运行，并且tty由具体的/dev/pts/num变为了？，此时仍是ok的

但是如果这时候把Xshell关了的话，python脚本就停止运行了

另外从`nohup.txt`可以看到这样的输出：`Fatal IO error: client killed`

## 解决办法

在换用vscode ssh连接之后，运行原python脚本报错：`[Python] RuntimeError: Invalid DISPLAY variable`

原因是matplotlib的默认backend是TkAgg，而FltkAgg, GTK, GTKAgg, GTKCairo, TkAgg , Wx or WxAgg这几个backend都要求有GUI图形界面的，所以在ssh操作的时候会报错．

所以我们需要指定不需要GUI的backend（Agg, Cairo, PS, PDF or SVG）

例如：
```python
import matplotlib
matplotlib.use('Agg')
```

修改之后换回Xshell，发现能正常exit，并且python脚本也仍在后台运行，解决问题

## 参考
[[Python] RuntimeError: Invalid DISPLAY variable](https://www.cnblogs.com/bymo/p/7447409.html)
## 附一个matplotlib内存泄漏的问题
使用**vscode的jupyter notebook**，在用matplotlib循环画图的时候会导致内存泄露
参考[matplotlib:绘制完成后释放内存](https://www.coder.work/article/2392503)和[使用matplotlib内存泄漏](https://www.jianshu.com/p/de0e69b8bec5)可以很大程度上解决

关键就是要记得写`plt.close('all')`以及`gc.collect()`

另外，可能有**运行环境**的部分原因在里面，使用如上解决办法之后内存泄漏大幅度降低，但是如果直接写成一个python脚本再运行的话，基本不会产生内存泄漏，具体原理机制暂时也不清楚，这里先记录下来
