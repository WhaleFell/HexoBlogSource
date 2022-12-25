---
title: Linux 运维笔记(持续更新)
date: 2022-03-20 16:37:48
updated: 2022-03-20 16:37:48
categories: Linux
tags: [Linux]
description: 
index_img: http://pic.lskyl.xyz//blog/img/Linux-Operation-Note-Banner.png-picsmall
banner_img: http://pic.lskyl.xyz//blog/img/Linux-Operation-Note-Banner.png-picsmall
---
# Linux Operation Note

![Banner](http://pic.lskyl.xyz//blog/img/Linux-Operation-Note-Banner.png-picsmall-picsmall)

## 硬盘操作

### 格式化硬盘为 ETX4 格式

```shell
mkfs.ext4 /dev/*** #将硬盘格式化成 ext4 格式
```

### 设置硬盘开机挂载

```shell
# 1.通过 lsblk 命令获取磁盘分区，并挂载磁盘到mnts目录。
lsblk

# 2.通过 blkid 命令获取存储设备UUID
blkid /dev/sda

# 3.编辑 /etc/fstab 文件，在fstab最后添加存储设备信息，实现开机自动加载
vim /etc/fstab
UUID=cdfc0924-7703-40f4-b6ab-8ef3f9adbf06 /mnt/disk ext4 defaults 0 0

# 4.通过mount -a命令重新加载 /etc/fstab 内容
mount -a

# 常用查询硬盘命令
fdisk -l
df -h
```

## 进程相关

### 开机自启动某个脚本

```shell
# 编辑 systemctl 文件
vim /etc/systemd/system/ttnode.service

# 文件内容
[Unit]
Description=Python Task Service
After=network.target

[Service]
Type=simple
User=root
Group=root
ExecStart=/usr/node/ttnode -p /mnt/disk/
Restart=always
RestartSec=2
PrivateTmp=true

[Install]
WantedBy=multi-user.target

# 重载
systemctl daemon-reload
# 启动
systemctl start ttnode
# 开机自启
systemctl enable ttnode
```

## 网络相关

### OpenVPN 配置

> 参考：[窗口 - 使用OpenVPN，我如何只能让局域网通过VPN？- 服务器故障 (serverfault.com)](https://serverfault.com/questions/785767/with-openvpn-how-can-i-only-let-lan-go-through-the-vpn) **重要**
>
> [ROUTE: route addition failed - lsgxeva - 博客园 (cnblogs.com)](https://www.cnblogs.com/lsgxeva/p/11378768.html)
>
> [networking - How to route only subnet in OpenVPN and not internet traffic - Super User](https://superuser.com/questions/1608345/how-to-route-only-subnet-in-openvpn-and-not-internet-traffic)

服务端：`server.conf`  

```ini
server 192.168.255.0 255.255.255.0
verb 3
key /etc/openvpn/pki/private/0.0.0.0.key
ca /etc/openvpn/pki/ca.crt
cert /etc/openvpn/pki/issued/0.0.0.0.crt
dh /etc/openvpn/pki/dh.pem
tls-auth /etc/openvpn/pki/ta.key
key-direction 0
keepalive 10 60
persist-key
persist-tun

proto tcp
# Rely on Docker to do port mapping, internally always 1194
port 1194
dev tun0
status /tmp/openvpn-status.log

user nobody
group nogroup
comp-lzo no

### Route Configurations Below
route 192.168.254.0 225.225.225.0

### Push Configurations Below
```

客户端：`client.conf`  

```ini
client
nobind
dev tun
remote-cert-tls server
remote tx.cyidz.xyz 11194 tcp

<key>
-----BEGIN PRIVATE KEY-----
....
-----END CERTIFICATE-----
</ca>
key-direction 1
<tls-auth>
#
# 2048 bit OpenVPN static key
#
-----BEGIN OpenVPN Static key V1-----
....
-----END OpenVPN Static key V1-----
</tls-auth>

# redirect-gateway def1  # 注释掉
dhcp-option DNS 114.114.114.114 # 自定义DNS
route 192.168.1.0 255.255.255.0  # 添加路由
route 192.168.0.0 255.255.255.0
```
