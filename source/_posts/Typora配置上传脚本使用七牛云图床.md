---
title: Typora配置上传脚本使用七牛云图床
toc: true
tags: [Linux]
categories: 记录
description: 利用Typora提供的上传图片功能，编写shell脚本，调用七牛云提供的qshell命令行工具，在windows环境下通过wsl2运行，将markdown文档中的本地或网络图片选择性地上传到七牛云图床。
cover: false
---
# Typora配置上传脚本使用七牛云图床

## Abstract

利用Typora提供的上传图片功能，编写shell脚本，调用七牛云提供的[qshell](https://github.com/qiniu/qshell)命令行工具，在windows环境下通过wsl2运行，将markdown文档中的本地或网络图片选择性地上传到七牛云图床。



## Prerequisites

### Typora Version

> ​	In newer version of Typora (≥ **0.9.9.32** on macOS or **0.9.84** on Windows / Linux), we added a “**upload image**” function to upload images to a cloud image storage via 3rd apps or scripts.               ——[official documentation](https://support.typora.io/Upload-Image/)

### [七牛云](https://www.qiniu.com/)

- 注册登录
- 控制台➡对象存储Kodo➡空间管理➡新建空间，要求名字唯一，访问控制设置为public
- 配置CDN加速域名，要求必须有一个备过案的域名
  - 可以直接绑定原域名，也可以绑定其二级域名
  - 根据个人情况选择。我选择绑定了原域名的二级域名**img.source_domain**
  - 绑定后需要到域名控制台配置CNAME解析
- 下载[qshell](https://github.com/qiniu/qshell)命令行工具，配置环境变量，下面几个命令的具体使用参考文档
  - 配置账户：`qshell account <Your AccessKey> <Your SecretKey> <Your Name>`
  - 分片上传文件：`qshell rput <Bucket> <Key> <LocalFile>` 
  - 抓取并存储：`qshell fetch <RemoteResourceUrl> <Bucket> [-k <Key>] `
  - 下载文件：`qshell get <Bucket> <Key> [-o <OutFile>]`
  - 查询信息：`qshell buckets` `qshell domains <Bucket>` `qshell listbucket <Bucket>` 
- 关于对象存储绑定域名：
  - 要访问存储资源，可以绑定CDN加速域名或者源站域名
  - 源站域名是直接访问存储源站（也就是Bucket）的，会产生外网流量，没有加速效果
  - CDN加速域名会利用边缘结点缓存加速，产生CDN流量和回源流量（没有缓存的话回源存储对应产生回源流量）
  - CDN加速域名绑定时存储源站可以选择对象存储（Bucket）（也就是在一个Bucket下绑定域名时的唯一选择），也可以选择自己的服务器（在CDN服务➡域名管理➡回源配置中有更多选项）



## Configuration

上传图片策略依托的是第三方工具或者脚本，这里考虑用脚本实现，即选择*Custom Command*，然后输入相应命令

**脚本规则：**会把N个图片路径跟在所指定的命令后面，要求执行命令时标准输出的最后N行是上传后的图片链接



在win环境下为了方便（写shell脚本并且使用windows路径），我的方法是，下载win下的[qshell](https://github.com/qiniu/qshell)，依托wsl2执行shell脚本，shell脚本再调用qshell.exe执行上传或抓取操作

### Shell Script

```bash
#!/bin/bash

set -e

BUCKET="blog"
QSHELL="qshell.exe"
DOMAIN="http://img.srouce_domain"

i=0

for filepath in ${@}; do
	i=$((${i}+1))
	date=$(date +'%Y%m%d-%H%M%S')
	
	# 如果路径中没/就找\
	if [ "${filepath}" == "${filepath##*/}" ]; then
		filename="${date}-${filepath##*\\}"
	else
		filename="${date}-${filepath##*/}"
	fi

    if [[ "${filepath}" =~ ^http.* ]]; then
    	# remote resource
    	${QSHELL} fetch "${filepath}" ${BUCKET} -k "${filename}" > /dev/null
    else
    	# local resource
    	${QSHELL} rput ${BUCKET} "${filename}" "${filepath}" > /dev/null
    fi
    
    if [ $? -eq 0 ]; then
    	if [ ${i} -eq 1 ]; then
    	echo "Upload Success:"
    	fi
    	echo "${DOMAIN}/${filename}"
    else
    	echo "Upload ${filepath} Failed!"
    	exit 1;
    fi
done
```

### Preference Panel

![image-20210821111732800](http://img.luooofan.site/20210823-142833-image-20210821111732800.png)

对于插入图片时是否上传图片可以**全局设置**或者**单文档设置**

- 全局设置：即将上图中的*插入图片时...*策略指定为上传图片
- 单文档设置：
  - 首先勾选*允许根据YAML设置自动上传图片*，然后如果在md文档开头**YAML front matter**中配置`typora-copy-images-to: upload`时，该文档将在插入图片时自动上传图片；不配置该行时将选择首选项中指定的策略，如上图中即为复制到指定路径
  - 也可以通过菜单配置（menu➡Format➡Image➡When insert local image…➡Upload Image）

**NOTE**：如果图片一开始都放在本地，然后通过菜单选择*上传所有本地图片*的话，可能需要本地图片所在目录没有中文，因为Typora在执行脚本的时候会自动把中文UrlEncode编码导致上面的脚本找不到本地图片位置

具体选择什么样的策略根据个人情况来定



## Knowledge

### CDN（Content Delivery Network）内容分发网络

CDN的出现是为了改善现有网络服务质量，它是构建在现有网络基础之上的智能虚拟网络，把内容缓存在终端用户附近，一个通俗**粗略**的理解是：

- 原来通过一个域名访问网站，先通过DNS服务器（并不限制为1台或1个集群）找到目标IP地址，然后与目标IP通信。

- DNS解析里可以配置A记录，也可以配置CNAME记录，A记录解析为目标IP，CNAME记录解析为另外一个域名。

- 现在加了CDN，把域名交给DNS服务器之后，找到CNAME记录解析到一个CDN专用DNS服务器，最终会返回一个CDN全局负载均衡设备IP给用户，用户与该设备通信，该设备通过一系列操作能获得一个计算得到的较优的目标IP，并返回给用户，用户最终与返回的这个目标IP通信。

使用 CDN 服务的网站，只需将其域名解析权交给 CDN 的全局负载均衡（GSLB）设备，将需要分发的内容注入CDN，就可以实现内容加速了。

### Shell

字符串截取：

- 指定位置和长度：`${str:[0-]start[:length]}` 
  - 加上 **0-** 是从右边从1开始计数，不加是从左边从0开始计数
  - 不加长度默认截取到字符串末尾
- 指定字符（子字符串）开始截取：`${str#pattern}`
  - **pattern**是匹配的模式，可以用 * 通配符
  - **#** 表示从左往右找到第一个匹配的，然后截取匹配字符串右边的所有字符
  - **##** 表示从左往右找到最后一个匹配的，然后截取匹配字符串右边的所有字符
  - **%** 表示从右往左找到第一个匹配的，然后截取匹配字符串左边的所有字符
  - **%%** 表示从右往左找到最后一个匹配的，然后截取匹配字符串左边的所有字符
  - 如果 * 位置没放对（按理应该返回空）或者没找到匹配字符串的话都会返回**整个str**



## Reference

1. [official documentation](https://support.typora.io/Upload-Image/)
2. [qshell](https://github.com/qiniu/qshell)
3. [Windows 与 Linux 的互操作性](https://docs.microsoft.com/zh-cn/windows/wsl/interop)
4. [Typora 自动上传图片到七牛云](https://razeencheng.com/post/typora-upload-image-qiniu.html)
5. [七牛云图床和Markdown使用](https://www.cnblogs.com/ssgeek/p/10854839.html)
6. [到底什么是CDN？](https://zhuanlan.zhihu.com/p/338951935)
7. [CDN 回源配置相关问题](https://developer.qiniu.com/fusion/kb/4064/understanding-and-setting-up-the-way-back-to-the-source-host)

