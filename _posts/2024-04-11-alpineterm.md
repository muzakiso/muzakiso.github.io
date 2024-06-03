---
layout: mypost
title: alpineterm
categories: [Linux]
extMath: false
---

## AlpineTerm使用教程
>上一篇文章介绍了如何使用termux和qemu来搭建alpine虚拟机进而使用docker，而alpineterm是GitHub上面的大神做的封装，使用更加方便
### 安装
alpineterm可以从GitHub上面进行下载，由于许久没有更新，请下载最新release即可，大概500MB左右
### 使用
1. 升级内核并更新系统
通过换源然后执行更新命令即可，初始的版本为3.10，升级过程：3.10－>3.14－>3.18
- 换源：vi /etc/apk/repositories
- 修改为https://mirrors.ustc.edu.cn/alpine/v3.14/main  https://mirrors.ustc.edu.cn/alpine/v3.14/community
- 更换dns： vi /etc/resolv.conf 修改为nameserver 8.8.8.8和8.8.4.4
- 更新：apk update && apk upgrade --available
- 重置：sync && reboot
- 检查内核：cat /etc/os-release && uname -r
*重复执行上面的命令以后，直至看到系统版本为3.18即可*
2. 安装docker
```bash
apk add docker
service docker start
rc-update add docker boot
```
3. 修改映射端口
当安装的容器需要通过端口访问时，由于无法直接访问qemu虚拟机的端口，因此我们需要端口映射，具体如下：
- 打开侧边栏选择qemu
- 执行命令hostfwd_add tcp::5700-:5700，修改为你想要映射的端口即可
- 通过主机的端口即可访问

**Written by Guyu**