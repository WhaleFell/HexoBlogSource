---
title: Clash 小猫咪使用备忘
date: 2023-07-09 10:26:56
updated: 2023-07-09 10:26:56
categories: Proxy
tags: [Proxy, Clash]
description: 
thumbnail: 
banner_img: 
---

# Clash 小猫咪使用备忘

Clash is a rule-based tunnel in Go.

> Clash is a **cross-platform** **rule-based** proxy utility that runs on the network and application layer, supporting various proxy and **anti-censorship protocols** out-of-the-box.
> 
> It has been adopted widely by the Internet users in some countries and regions **where the Internet is heavily censored or blocked.**

**China Anti-censorship**: [censorship.ai | 揭示和规避中国对加密SNI（ESNI）的封锁](https://geneva.cs.umd.edu/zh/posts/china-censors-esni/esni/)

> anti-censorship /ˌæn.tiˈsen.sə.ʃɪp/ adj. 反审查 [cambridge dictionary](https://dictionary.cambridge.org/zhs/%E8%AF%8D%E5%85%B8/%E8%8B%B1%E8%AF%AD/anti-censorship)

## Document

GitHub: [GitHub - Dreamacro/clash: A rule-based tunnel in Go.](https://github.com/Dreamacro/clash)  
official document: [What is Clash? | Clash](https://dreamacro.github.io/clash/)  
[Clash for Windows](https://docs.cfw.lbyczf.com/) config file: [配置文件 | Clash for Windows](https://docs.cfw.lbyczf.com/contents/configfile.html)

## Use Client

## Use Raw Console Program

The main configuration file is called `config.yaml`.  
Use command-line option `-d` to specify a configuration directory.  
use option `-f` to specify a configuration file

```shell
clash -d . # current directory
clash -d /etc/clash

clash -f ./config.yaml
clash -f /etc/clash/config.yaml
```

## OpenWRT OpenClash

[Releases · vernesong/OpenClash](https://github.com/vernesong/OpenClash/releases)  
[Home · vernesong/OpenClash Wiki · GitHub](https://github.com/vernesong/OpenClash/wiki)

内核文件下载  
[Release Clash · vernesong/OpenClash · GitHub](https://github.com/vernesong/OpenClash/releases/tag/Clash)

Dev 内核下载: <https://github.com/vernesong/OpenClash/releases/tag/Clash>  
Tun 内核下载: <https://github.com/vernesong/OpenClash/releases/tag/TUN-Premium>  
Tun 游戏内核: <https://github.com/vernesong/OpenClash/releases/tag/TUN>

### INSTALL

```shell
opkg list | grep "clash"

opkg remove luci-app-clash --autoremove
opkg remove luci-app-openclash --autoremove

#iptables
opkg update
opkg install coreutils-nohup bash iptables dnsmasq-full curl ca-certificates ipset ip-full iptables-mod-tproxy iptables-mod-extra libcap libcap-bin ruby ruby-yaml kmod-tun kmod-inet-diag unzip luci-compat luci luci-base

cd /tmp/
wget ...
opkg install ...ipk

wget https://github.com/vernesong/OpenClash/releases/download/Clash/clash-linux-386.tar.gz
tar -zxvf .
chmod 777 
```

>curl 故障：  
>重新安装 libmbedtls  
[[Bug] 升级后更新功能错误 · Issue #3386 · vernesong/OpenClash · GitHub](https://github.com/vernesong/OpenClash/issues/3386)

## Config File

## Fake-IP

ref: [浅谈在代理环境中的 DNS 解析行为 | Sukka's Blog](https:/blog.skk.moe/post/what-happend-to-dns-in-proxy/)

在 Client 端设置 Socks5 代理的分流过程：  
![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/img/202307091454148.png&webp=true)

但是使用 Route 透明代理、全局 tun/tun2socks 代理时，此时应用程序是 **不会感知到代理客户端的存在**，它们会正常的 **发起 TCP 连接**，并且由于 TCP/IP 协议，在拿到 DNS 解析结果之前，连接是不能建立的。  

> **在应用发起 TCP 连接时，会先发出一个 DNS question（发一个 IP Packet），获取要连接的服务器的 IP 地址，然后直接向这个 IP 地址发起连接。**  
> 					                                                                                           **—— TCP/IP 协议**

当 Proxy 运行在 tun 模式的透明代理时：  
![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/img/202307091516885.png&webp=true)

和应用程序直接将流量 **封装成 SOCKS5** 大有不同，在类似于透明代理的环境下浏览器和其它应用程序是正常地发起 TCP 连接。因此需要得到一个 DNS 解析结果，才能建立 TCP 连接；代理客户端也会需要通过这个 DNS 查询动作，才能找到之后的 TCP 连接的域名，进行分流。

浏览器、应用程序直接设置 SOCKS5 代理的话，**可以不在代理客户端发起 DNS 解析请求** 就能将流量发送给远端服务器；而在 **透明代理模式** 下，不论是否需要 IP 规则分流都需要**先进行一次 DNS 解析**才能建立连接。  

**而且 DNS 解析的过程在 Proxy Server 上实现，远端服务器只能获取到解析出来的 IP，对于 CDN 优化不好。**

如果是支持 **redir** 的代理客户端，那么代理客户端就会直接将 **域名** 和 **TCP content** 封装成 某种协议 发给远端服务器，**总之解析域名 DNS 和请求将在远端服务器进行，最大程度优化 CDN 速度**。

[Fake-ip 和 Redir-host 两个模式，从速度上看，那个有优势？如果综合看那个更有优势？ · Issue #974 · vernesong/OpenClash · GitHub](https://github.com/vernesong/OpenClash/issues/974)

Fake-IP 实现过程：  
![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/img/202307091530969.png&webp=true)

**Fake-IP** 能够省下 Client 向远端 DNS 请求 IP 的过程，就是代理客户端自己不先执行查询动作，丢一个 Fake IP 回去让浏览器、应用程序立刻建立 TCP 连接。

**Fake-IP** 技术更加适合根据 **IP 规则分流、Route 透明代理** 的情景，如果应用程序直接将流量封装成 SOCKS5 可以不在 Client 发起 DNS 请求就能把浏览转发到 Proxy Server.而在透明代理模式下，不论是否需要 IP 规则分流都需要先进行一次 DNS 解析才能建立连接。  

**Fake-IP** 可以使域名解析在远端服务器上进行，能有效优化 DNS 解析速度。

> 实际使用 Fake-IP 可能导致映射表丢失，导致浏览器或者本地缓存了错误的 DNS 解析结果比如 `192.168.16.7 --> google.com` **这时需要清除 DNS 缓存**才行。
