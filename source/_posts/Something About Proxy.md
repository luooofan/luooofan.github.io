---
title: Something About Proxy
date: 2021-02-21 04:36:47
categories: Network
tags: [Proxy]
description: 关于代理的一些理解和配置
keywords: 
cover: false
---
# Something About Proxy

&emsp;&emsp;Web 代理是一种存在于网络中间的实体，提供各式各样的功能。现代网络系统中，Web 代理无处不在。

&emsp;&emsp;比较常见的HTTP代理和[Socks](https://zh.wikipedia.org/wiki/SOCKS)代理。HTTP代理存在两种形式，一种是普通代理，扮演中间人角色，只能提供HTTP服务。另一种是隧道代理，它通过 HTTP 协议正文部分（Body）完成通讯，以 HTTP 的方式实现任意基于 TCP 的应用层协议代理，这种代理使用 HTTP 的 CONNECT 方法建立连接。

## Windows系统代理设置

&emsp;&emsp;Windows支持系统代理设置，最原先好像是IE选项中的代理设置。支持使用设置脚本（PAC），也支持手动设置代理。手动设置默认配置的是HTTP代理，未配置SOCKS5。使用PAC脚本的话则可按优先级设置HTTP、SOCKS5代理以及直连。关于PAC可以参考[详解代理自动配置 PAC](https://www.barretlee.com/blog/2016/08/25/pac-file/)。

&emsp;&emsp;需要注意的是，尽管设置的是系统代理，但并非所有的流量都会经过，取决于具体的软件，是否使用系统代理设置。所以一般来说进行配置之后只能保证浏览器会走代理。

&emsp;&emsp;如果我要用`wget`或者`curl`或者其他终端软件设置代理访问站点，可以配置终端代理。cmd、powershell、linux shell、git等的配置都有所不同，可以参见[在 Windows 终端中设置代理](https://www.yixuju.cn/other/talking-about-proxy/)和
[Win/Linux 命令行、终端和 Git 代理设置](https://g2ex.top/2017/10/22/windows-linux-git-proxy-cmd/)
## 对clash中内置dns server的理解

首先clash中内置的这个模块是一个dns服务器模块，一般情况下没必要配置。

在未使用该模块的情况下，clash是直接使用系统DNS进行域名解析的。

经过clash的流量有以下这几种情况：

1. 根据域名规则匹配，如果要走代理的话，那么这个DNS解析过程是多余的；
2. 根据域名规则匹配，如果决定直连，那么会使用到这个DNS解析的结果；
3. 如果要根据ip规则进行匹配的话，会根据这次DNS解析出来的ip地址进行ip规则匹配。走代理的话，不会用到这个ip；直连的话使用这个ip；
4. 各种规则都没匹配到，走MATCH规则。

从上面可以看出来，clash中的DNS server的作用有：

1. 进行直连（DIRECT）时，解析域名与远端连接
2. 解析域名，判断是否命中IP类规则

此外，还有一个作用：作为一个DNS调度服务器，个人认为是可以设置多个dns服务器并发请求，模块内部实现了调度功能。

&emsp;&emsp;有一点要注意，如果流量最终走代理的话，**Clash实际上并不会向代理服务器发送IP地址，而是直接将host发送给代理服务器，在代理服务器中进行DNS解析，这样可以让请求获取到更合适的IP地址，更好的利用CDN资源**。也就是说如果最终根据域名规则或者ip规则决定走代理的话，本地进行的dns解析得到的ip地址实际上并未使用.

&emsp;&emsp;所以如果要考虑dns污染问题的话，必须是dns被污染了且经过[重重规则](https://github.com/Dreamacro/clash/wiki/configuration#rules)之后决定直连，这种情况下才会影响。但是事实上经过各种域名类规则、IP类规则、MATCH规则后，仍然满足条件的少之又少。

&emsp;&emsp;在路由器或虚拟机中使用Clash做透明代理时，需要使用Clash内置的DNS服务器进行解析，DNS服务器会生成IP和域名的关系，从而在Clash端反推对应的域名（因为redir只能传递IP）。此时必须要配置DNS服务器，并且其他设备要使用此DNS服务器。具体配置项可以参考[这份文档](https://lancellc.gitbook.io/clash/clash-config-file/dns)。


### 相关

- [DNS污染对Clash（for Windows）的影响 from cfw wiki](https://github.com/Fndroid/clash_for_windows_pkg/wiki/DNS%E6%B1%A1%E6%9F%93%E5%AF%B9Clash%EF%BC%88for-Windows%EF%BC%89%E7%9A%84%E5%BD%B1%E5%93%8D)
- [dns中fallback的作用 from clash issues#642](https://github.com/Dreamacro/clash/issues/642)
- [有关clash dns 解析中fallback组的意义的疑问 from clash issues#968](https://github.com/Dreamacro/clash/issues/968)
- [浅谈在代理环境中的 DNS 解析行为 | Sukka's Blog](https://blog.skk.moe/post/what-happend-to-dns-in-proxy/)
- [代替 Surge 增强模式——使用 KoolClash 作为代理网关](https://blog.skk.moe/post/alternate-surge-koolclash-as-gateway/)
- [透明代理](https://legacy.lightquantum.me/posts/transparent-proxy/)


## IDM的代理设置

&emsp;&emsp;IDM可以设置使用系统代理或者手动配置代理，设置代理的时候可以直接点击从IE获取，这样会自动选择配置的系统代理或者PAC。

&emsp;&emsp;不过只设置代理有时候也不行，因为IDM会使用系统DNS进行域名解析，会遇到DNS污染的情况。比如下载一个来自`raw.githubusercontent.com`的文件，直接解析的话会返回`0.0.0.0`或者本地。所以还要配置一下DNS，可以直接修改系统DNS或者路由器DNS为可信度更高的DNS服务器。如果使用Clash的话，也可以开启内置的DNS server，然后把系统DNS指向本地，IPv4 DNS服务器配置127.0.0.1，IPv6 DNS服务器配置::1，也就是DNS请求走本地53端口。

&emsp;&emsp;另外，IDM也是支持命令行启动的，把idman.exe配置到环境变量。常见的参数：/d URL，/n，静默下载模式，/p，指定本地路径，/f，指定本地文件名，/a，添加一个指定的文件到下载队列，但是不开始下载，/s，开始任务调度里的队列，/q，IDM在下载成功后自动退出，/h，IDM在下载成功后挂起连接。可以写一个bat文件来实现静默批量下载。

----------



## 参考

- [Clash](https://github.com/Dreamacro/clash)
- [Clash for Windows](https://github.com/Fndroid/clash_for_windows_pkg)
- [Unofficial Clash Wiki.](https://lancellc.gitbook.io/clash/)
- [CFW中文文档](https://docs.cfw.lbyczf.com/)
- [HTTP 代理原理及实现（一）](https://imququ.com/post/web-proxy.html)
- [A cartoon intro to DNS over HTTPS](https://hacks.mozilla.org/2018/05/a-cartoon-intro-to-dns-over-https/)

