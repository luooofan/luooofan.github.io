---
title: 华为云配置mmdetection记录
date: 2021-04-01 19:15:52
categories: Server
tags: [记录, Linux]
description: 这几天在华为云上配置mmdetection，踩了很多坑，记录一下
keywords: 
cover: false
---
# 华为云配置mmdetection记录
这几天在华为云上配置mmdetection，踩了很多坑，记录一下


## 云资源使用

- 按需计费的云服务器一般都挂着按需计费的云硬盘（系统盘），绑定着公网IP（IP和带宽按需计费）
- 公网IP的购买，绑定，解绑，释放都非常方便
- 公网IP创建的时候可以不选，这样的话服务器不能访问外网，我们也不能通过ssh root@ip来登录，只能在华为云这边登录服务器
- 如果初始不选公网IP的话，要选择密码认证，别选密钥认证
- 选择云服务器的时候按需选择，比如要用mmdetection跑模型，就选GPU加速型的，GPU加速型里又要看GPU的类型、块数、显存，有的模块方法不支持多块GPU一起跑[(mmdet MMDataParallel)](https://github.com/open-mmlab/mmdetection/issues/3116)，有的训练集和模型根本用不到32G显存，16G足够了
- 可以对配好环境的服务器系统盘做系统盘镜像，购买其他高配置服务器的时候直接从私有镜像创建即可，不过要注意换了高配置GPU之后原来安装的模块可能会出现不兼容的问题
- 做快照在之后出问题的时候可以用来恢复之前的环境

## CUDA
- nvcc命令
- nvidia-smi命令
- 安装pytorch的时候先看CUDA版本
- 环境里有多个CUDA版本的时候记得在.bashrc中配置好环境变量
- CUDA_VISIBLE_DEVICES环境变量
- [keras or tensorflow 显存占用问题](https://www.jianshu.com/p/3f8f46b0b1f2)，默认会尽量占满显存，可以限制


## [mmdetection](https://github.com/open-mmlab/mmdetection)

**安装的时候一定要好好看文档！！！**
**要特别注意各个库版本之间的对应关系！！！**
- [colab tutorial](https://github.com/open-mmlab/mmdetection/blob/master/demo/MMDet_Tutorial.ipynb)中暂时仍有几处错误，遇到可以查issues，里面有解决方案
- 不用单独装mmcv，安装mmcv-full就可以了
- 如果之后报错没有mmcv-ext这个模块，我的解决办法是从源代码编译安装mmcv-full，[issues3271](https://github.com/open-mmlab/mmdetection/issues/3271)

- mmcv还依赖于GPU，如果换了块GPU，出现no kernel image的报错，可以考虑pip uninstall然后重新install mmcv-full
- 使用教程[【mmdetection】使用：训练和测试](https://blog.csdn.net/weixin_39916966/article/details/101543005)
- 预训练模型.pth文件，默认是运行时从链接即时下载的，也可以提前下载到本地之后在config文件里指定路径pretrain
- 参考[FAQ:pytorch-cuda-env](https://mmdetection.readthedocs.io/en/latest/faq.html#pytorch-cuda-environment)

最后详细说下这个错误：RuntimeError: CUDA error: no kernel image is available for execution on the device  [issues4335](https://github.com/open-mmlab/mmdetection/issues/4335)(实际上有好多issue指向这个错误，是由很多种情况造成的)
- 发现这个报错可以先试一下这个[pytorch issues31285](https://github.com/pytorch/pytorch/issues/31285)，总的来说是GPU和torch之间的不兼容，大部分情况是GPU太老了，当然从issues中也能看到新的GPU也会遇到这个问题
- 如果发现虽然也有这个报错，但无法通过上面那个issue复现的话，那很有可能是GPU和mmmcv之间的不兼容导致的，可以把mmcv和mmdet uninstall之后重新安装
- 此外从issues中还能看到因为有多个CUDA版本未正确配置的情况、pytorch版本过高的情况等等

总之，要多看文档，多查issue
