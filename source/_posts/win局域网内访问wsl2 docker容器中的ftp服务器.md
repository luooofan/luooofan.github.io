---
title: win局域网内访问wsl2 docker容器中的ftp服务器
date: 2021-07-04 12:18:38
categories: Server
tags: [wsl2, docker, 记录]
description: win局域网内访问wsl2 docker容器中的ftp服务器
keywords: 
cover: false
---
# win局域网内访问wsl2 docker容器中的ftp服务器
这两天遇到这样一个需求，将win上wsl2里docker的某ubuntu容器中的文件发送到和win在同一个局域网下的树莓派。

有很多种解决方案，综合考虑之后选择了在容器里搭建ftp服务器，然后树莓派使用curl来下载ftp服务器上的文件这种方式来实现。

整个过程中有这么几个实体，每个实体都有相应的配置或操作：
- raspberry pi
- win
- wsl2
- docker
- ubuntu container

## WSL2网络
wsl2和win是一个局域网内的两个ip，这个局域网是由 Hyper-V 创建的
wsl2使用的网络适配器每次重启都会被删除重建，所以其IP不固定

**配置指向win的代理**：
首先获取win的ip，然后在.zshrc文件中配置代理，同时win上的代理要允许局域网访问

```bash
host_ip=$(cat /etc/resolv.conf | grep "nameserver" | cut -f 2 -d " ")
export ALL_PROXY="http://$host_ip:7890"
export HTTP_PROXY=$ALL_PROXY
export http_proxy=$ALL_PROXY
export HTTPS_PROXY=$ALL_PROXY
export https_proxy=$ALL_PROXY
git config --global http.proxy "socks5://$host_ip:7890"
git config --global https.proxy "socks5://$host_ip:7890"
sed -i "s/\(:\/\/\)\(.*\)\(:7890\)/\1$host_ip\3/g" ~/.condarc
sed -i "s/\(:\/\/\)\(.*\)\(:7890\)/\1$host_ip\3/g" ~/.config/pip/pip.conf
```


## Docker
主要是创建一个能用clang的ubuntu20.04镜像，同时换源，安装好vim、git、systemctl等

Dockerfile:
```bash
FROM ubuntu:20.04

RUN sed -i s@/security.ubuntu.com/@/mirrors.tuna.tsinghua.edu.cn/@g /etc/apt/sources.list \
&& sed -i s@/archive.ubuntu.com/@/mirrors.tuna.tsinghua.edu.cn/@g /etc/apt/sources.list \
&& apt-get update \
&& apt-get install -y systemctl vim build-essential clang-10 git libantlr4-runtime-dev \
&& ln -s /usr/bin/clang-10 /usr/bin/clang \
&& ln -s /usr/bin/clang++-10 /usr/bin/clang++

# Start from a Bash prompt
CMD [ "/bin/bash" ]
```

用这个dockerfile创建镜像，然后创建一个容器
因为容器内要搭建ftp服务，所以需要相应的容器端口和wsl2端口映射
`docker build -t ubuntu_clang .`
```bash
docker run -itd \
--name compiler2021 \
-v /mnt/path/to/wsl_docker:/root/wsl_docker \
-p 20:20 \
-p 21:21 \
-p 21100-21110:21100-21110 \
ubuntu_clang
```

配置docker服务和该容器的自启动，在.zshrc中添加
`sudo service docker start > /dev/null`
`docker start compiler2021 > /dev/null`


## 容器配置
### ubuntu安装配置ftp服务器
`apt-get install vsftpd`
`vim /etc/vsftpd.conf`
```bash
userlist_deny=NO
userlist_enable=NO

userlist_file=/etc/vsftpd.user_list

seccomp_sandbox=NO
local_enable=YES
write_enable=YES

utf8_filesystem=YES


anonymous_enable=YES

port_enable=NO

pasv_enable=YES
pasv_min_port=21100
pasv_max_port=21110
```

### 配置服务自启动

`systemctl enable vsftpd`
`systemctl start vsftpd`
`systemctl status vsftps`

### 测试本机能否访问
在容器内和wsl2内可以`ftp localhost`
在win上需要用文件资源管理器访问，命令行ftp不支持被动模式
测试时一看能不能开始登录（网络问题），二看能不能登录成功（配置问题），三看登录成功后能不能正常ls（主动模式还是被动模式），ftp报什么错误就直接去搜，网络配置ok的话，大部分都是vsftpd配置上的问题



## Win配置端口转发
树莓派无法直接访问wsl2所在的局域网，只能访问到win，所以需要将局域网内访问win ftp端口的流量转发到wsl2相应的端口，wsl2相应的端口映射到docker容器相应的端口，最终完成ftp服务器的访问

端口转发：
```powershell
# All the ports you want to forward separated by coma
# $ports=@(80,443,8080);
$ports=@(21100..21110);
$ports+=21;

# [Static ip]
# You can change the addr to your ip config to listen to a specific address
$addr='0.0.0.0';
# $addr='192.168.31.88';
$ports_a = $ports -join ",";

# 开放防火墙

for( $i = 0; $i -lt $ports.length; $i++ ){
    $port = $ports[$i];
    iex "netsh interface portproxy delete v4tov4 listenport=$port listenaddress=$addr"  | Out-Null
    iex "netsh interface portproxy add v4tov4 listenport=$port listenaddress=$addr connectport=$port connectaddress=$wslip"  | Out-Null
}
```

开放防火墙：
```powershell
# Remove Firewall Exception Rules
# for 'vEthernet(WSL)' use 'WSL' not 'WSL 2 ...'
iex "Remove-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' " | Out-Null

# Adding Exception Rules for inbound and outbound Rules
iex "New-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' -Direction Outbound -LocalPort $ports_a -Action Allow -Protocol TCP"  | Out-Null
iex "New-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' -Direction Inbound -LocalPort $ports_a -Action Allow -Protocol TCP"  | Out-Null
```

创建一个wsl2.ps1`powershell`脚本，配置为wsl2启动时计划执行，具体配置过程参见[WSL2 的一些网络访问问题](https://lengthmin.me/posts/wsl2-network-tricks/)
过程中修改任务计划程序属性时可能提示**一个或多个指定的参数无效**，其实是用户账户的问题，重新搜索填一下用户账户即可
另外，参数指定了`-WindowStype Hidden`的表现好像是powershell窗口依旧弹出，但即刻关闭


## 树莓派
### 前置工作
- 贴散热片，装壳
- 往sd卡里写入raspberrypi系统
- 在sd卡根目录下(/boot/)创建wpa_supplicant.json文件和ssh文件，在前者里配置wifi
  注意，可能只支持2.4G，不支持5Gwifi
  如果开机后没连成功wifi，可以用网线和笔记本相连，两者共用以太网
- 插sd卡，插电源开机
- 使用`nmap -sn 192.168.xxx.0/24`扫描局域网，推断出树莓派的ip
- 使用putty、xshell或mobaxterm登录树莓派
- 换清华源
- 查看系统版本`cat /etc/os-release`，`uname -a`，`lsb_release -a`
- 查看cpu信息`cat /proc/cpuinfo`

wpa_supplicant.json:
```json
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=CN
network={
        ssid="宿舍wifi"
        key_mgmt=WPA-PSK
        psk="passwd"
        priority=10
}
network={
        ssid="手机热点"
        psk="passwd"
        key_mgmt=WPA-PSK
        priority=5
}

```

### 网络配置命令

`ifconfig`
`sudo ifconfig wlan0 down/up`
`iw wlan0 link`
`iw wlan0 scan`
`sudo wpa_supplicant -B -i wlan0 -c /etc/wpa_supplicant/wpa_supplicant.conf`

### 下载目标文件
因为是ftp协议，所以使用wget和curl都可以
curl命令如下：
`curl ftp://ftp:passwd@192.168.xxx.xxx/ -s` 查看目录
`curl ftp://ftp:passwd@192.168.xxx.xxx/test.txt -o target.txt` 下载文件
当然，也可以使用ftp命令访问服务器

### 关机和重启
`sudo shutdown -h now`
`sudo reboot`


## References
[WSL2 的一些网络访问问题](https://lengthmin.me/posts/wsl2-network-tricks/)
[linux自启动服务的几种方式](https://www.jianshu.com/p/1dd924ae2e05)
[使用curl下载上传ftp](http://bolg.malu.me/html/2011/1239.html)
[Docker一行命令完成FTP服务搭建](https://juejin.cn/post/6844903943122714637)
[微软文档powershell about arrays](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_arrays)
[WSL2中安装docker 并开机启动](https://www.jianshu.com/p/244a9d8a2309)
[Linux 使用wpa_supplicant手动配置连接wifi](https://www.cnblogs.com/hokori/p/14168584.html)
[树莓派连接WIFI无线网络配置](https://segmentfault.com/a/1190000017457004)
[Raspbian 镜像使用帮助](https://mirror.tuna.tsinghua.edu.cn/help/raspbian/)
[How to Fix “W: Some index files failed to download.” Error In Ubuntu](https://www.tecmint.com/some-index-files-failed-to-download/)

