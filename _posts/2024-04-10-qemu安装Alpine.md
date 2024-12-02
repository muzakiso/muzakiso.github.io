---
layout: mypost
title: qemu安装Alpine
categories: [Linux]
extMath: false
---

### 下载镜像
虚拟机执行如下命令安装镜像，也可以安装新一点的镜像：
```shell
wget https://mirrors.aliyun.com/alpine/v3.15/releases/x86_64/alpine-extended-3.15.0-x86_64.iso
```
### 设置
设置镜像：
```shell
qemu-img create -f qcow2 alpine.qcow2 16G
qemu-img convert -f raw -O qcow2 alpine-extended-3.15.0-x86_64.iso alpine.qcow2
qemu-img resize alpine.qcow2 +20G
qemu-system-x86_64 -m 2048 -cdrom alpine.qcow2 -boot d -enable-kvm -vga std -display sdl -usb -usbdevice tablet -net nic,model=virtio -net user,hostfwd=tcp::2222-:22 -vnc :0
# 登录
ssh root@127.0.0.1 -p 2222
```
### 安装
```shell
apk update
apk add vim
apk add openssh
apk add openssh-server
echo "root:123456" | passwd root
systemctl enable sshd
systemctl start sshd
exit
ssh root@127.0.0.1 -p 2222
```
### 配置
```shell
cat <<EOF > /etc/ssh/sshd_config
Port 22
HostKey /etc/ssh/ssh_host_rsa_key
HostKey /etc/ssh/ssh_host_ecdsa_key
```
**Written by Guyu**

