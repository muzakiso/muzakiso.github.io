---
layout: mypost
title: termux docker
categories: [Linux]
extMath: false
---

### 操作前提
由于大部分手机的内核并不满足使用docker的要求，因此需要用root权限修改手机部分kernel来满足，但是手机root并不适用所有手机，而且root也有一定的风险，因此使用termux终端来通过qemu虚拟机进行容器化操作可以实现满足docker运行的要求。
### 操作步骤
1. 安装termux或者zerotermux或者其他类似终端
该软件作为开源项目可从github或gitee等网站进行下载
2. 安装qemu虚拟机
进入termux终端后进行换源并安装qemu套件
```bash
sed -i 's@^\(deb.*stable main\)$@#\1\ndeb https://mirrors.tuna.tsinghua.edu.cn/termux/termux-packages-24 stable main@' $PREFIX/etc/apt/sources.list
apt update && apt upgrade
apt install qemu*
wget https://mirrors.aliyun.com/alpine/v3.15/releases/x86_64/alpine-extended-3.15.0-x86_64.iso
```
3. 创建虚拟磁盘并安装alpine虚拟机
```bash
mkdir alpine && cd alpine
qemu-img create -f qcow2 alpine.qcow2 50G
qemu-system-x86_64 -smp 2 -m 2048 \
  -drive file=alpine.qcow2,if=virtio \
  -netdev user,id=n1,hostfwd=tcp::2222-:22 \
  -device virtio-net,netdev=n1 \
  -cdrom alpine-virt-3.15.0-x86_64.iso -boot d \
  -nographic
```
*此时进入alpine安装过程，建议参考官方wiki：*
```
https://wiki.alpinelinux.org/wiki/Install_Alpine_on_VMware_Workstation
https://wiki.alpinelinux.org/wiki/Installing_Alpine_in_a_virtual_machine
```
4. 编写开机脚本
```bash
nano run.sh
```
填入以下内容：
```bash
qemu-system-x86_64 -smp 2 -m 2048 \
  -drive file=alpine.qcow2,if=virtio \
  -netdev user,id=n1,hostfwd=tcp::2222-:22 \
  -device virtio-net,netdev=n1 \
  -nographic
```
5. 重启虚拟机并安装docker
```bash
bash run.sh
apk add docker && apk add docker-compose
service docker start
rc-update add docker boot
docker run hello-world
```
至此，可以愉快的使用docker了
### 反思与总结
我本人尝试了许多次手机安装docker的操作，这种借助qemu虚拟机的方法十分局限，因为qemu无法调用kvm，所以十分卡顿，建议仅作为一种尝试，并不建议日常使用

**Written by Guyu**