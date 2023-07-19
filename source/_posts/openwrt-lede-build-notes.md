---
title: Openwrt LEDE 固件自定义编译笔记
date: 2023-07-15 14:49:21
updated: 2023-07-15 14:49:21
categories: OpenWRT
tags: [OpenWRT, Linux]
description: 
thumbnail: 
banner_img: 
---

# Openwrt LEDE 固件自定义编译笔记

LEDE 有些软件 Openwrt 没有。比如我认为很重要的硬解 nat。lean 对国内用户也比较友善。

LEDE 项目地址：[GitHub - coolsnowwolf/lede: Lean's LEDE source](https://github.com/coolsnowwolf/lede)

## 参考

[看完这篇，自定义 OpenWrt/LEDE 路由固件不求人 - 少数派](https://sspai.com/post/61463)

## 编译前的准备

Dedian11、Ubantu18 以上的系统，根据文档安装好依赖。保持使用 Clash Tun 模式进行代理，在 Vmare 虚拟机上进行。

```shell
# 测试编译环境

make defconfig
```

## 自定义固件

### 根据菜单自定义

```shell
make menuconfig
```

```text
Target System (x86)  --->   目标系统（x86）  
Subtarget (x86_64)  --->   子目标（x86_64）  
Target Profile (Generic)  --->目标配置文件（通用）  
Target Images  ---> 保存目标镜像的格式  
Global build settings  --->      全局构建设置  
Advanced configuration options (for developers)  ---- 高级配置选项（适用于开发人员）  
Build the OpenWrt Image Builder 构建OpenWrt图像生成器  
Build the OpenWrt SDK  构建OpenWrt SDK  
Package the OpenWrt-based Toolchain 打包基于OpenWrt的工具链  
Image configuration  --->图像配置  
Base system  --->     基本系统  
Administration  --->     管理  
Boot Loaders  ---> 引导加载程序  
Development  --->   开发  
Extra packages  --->  额外包  
Firmware  --->固件  
Fonts  --->字体  
Kernel modules  --->  内核模块  
Languages  --->语言  
Libraries  --->  图书馆  
LuCI  --->      LuCI  
Mail  ---> 邮件  
Multimedia  --->多媒体  
Network  --->网络  
Sound  ---> 声音  
Utilities  --->实用程序  
Xorg  --->Xorg
```

```shell
## 选择系统(以 x86_64 为例)
Target System -> x86
Subtarget -> x86_64

## 选择固件的文件系统
## https://openwrt.org/docs/techref/filesystems
Target Images -> squashfs

## 选择构建X86_X64的GRUB固件
Target Images -> Build GRUB images (Linux x86 or x86_64 host only) 

## 选择更小的压缩格式固件，方便复制
Target Images -> GZip images

## 修改软件包可用空间，默认安装会占用100M左右，建议修改扩大，为后续安装其他软件打基础
Target Images -> Root filesystem partition size

## 添加web界面(y键选择n键排除)
LuCI > Collections -> Luci

## 添加兼容性依赖
LuCI > Modules -> luci-compat

## 添加中文
LuCI > Modules -> Translations -> Chinese Simplified

## 添加openclash
LuCI > Applications -> luci-app-openclash  

## 添加主题
LuCI -> Themes

## 添加wget
Nerwork -> File Transfer -> wget-ssl

## 添加kmod-tun，TUN模式必须
Kernel modules -> Network Support -> kmod-tun

## 排除dnsmasq，由于默认会安装dnsmasq-full，这里需要排除dnsmasq，否则会冲突报错。
Base system -> dnsmasq 

```

选择 docker

Base system  
取消选中 dnsmasq  
选中 dnsmasq-full  
因为 dnsmasq-full 支持 ipset 功能，对于基于域名的 xx 很有用。

Network->File Transfer 中选中 ssh ntp  
curl  
wget  
【两个下载工具】

Network->IP Addresses and Names 中选中  
bind-dig  
ddns-scripts_No-IP_com。（用来支持 no-ip.com 的 ddns 服务）  
一个是测试工具；另一个是某个 ddns 支持，还有其他 ddns 支持，也可以酌情选中。

Network->Routing and Rediction 中选中  
ip-full  
这个很关键。  
Network 中选中  
iperf3  
ipset  
一个是测试工具；  
另一个是 ipset，用于支持基于域名的 xx。

Network->Web Servers/Proxies 选中那些 SS，如果您需要使用 SS 的话。  
Utilities->Editors 中选  
nano （也可以选 vim）  
Utilities->Shells 中选中  
bash  
点击 Save，保存为.config。  
一路 Exit 退出。在输入 make 开始编译。

### 源码修改

修改默认语言主题：  
修改 vim feeds/luci/modules/luci-base/root/etc/config/luci 文件:

```yaml
config core main
        option lang "zh_cn"
        option mediaurlbase /luci-static/material
        option resourcebase /luci-static/resources
               
config internal languages
        option zh_cn "普通话 (Chinese)"
```

修改主机名，设定时区，IP 地址:

vim package/base-files/files/bin/config_generate

```yaml
generate_static_system() {
uci -q batch <<-EOF
delete system.@system[0]
add system system
set system.@system[-1].hostname='LEDE'
set system.@system[-1].timezone='CST-8'                      #东八区
set system.@system[-1].zonename='Asia/Shanghai'       #这句话要加上，不然还是UTC
lan) ipad=${ipaddr:-"192.168.8.1"} ;;       #修改默认IP
```

SSH/TELNET 显示信息修改方式:  
vim package/base-files/files/etc/banner

修改路由连接数  
vim package/base-files/files/etc/sysctl.conf  
net.netfilter.nf_conntrack_max=65535

[Openwrt 编译，修改lan、wan口，ip，拨号 - 简书](https://www.jianshu.com/p/27c424562de7)  
拨号

### 编译 OpenClash 小猫咪

[编译带 OpenClash 的 OpenWrt 固件 | 心底的河流](https://lhy.life/20200531-openwrt+openclash/)

## 行为管控

```shell
cd /home/user/lede/package
git clone https://github.com/destan19/OpenAppFilter.git

# 去除加速模块
rm package/lean/luci-app-flowoffload -fr
rm package/lean/shortcut-fe/ -fr
rm package/lean/luci-app-sfe/ -fr
```

## 编译

[openwrt编译openclash-老徐小屋](http://www.laoxu.date/html/202204/1912.html)

```shell
./scripts/feeds update -a
./scripts/feeds install -a

make defconfig

```

## 使用 GitHub Action

使用 p3terx 大佬的脚本。

[GitHub Actions 在线云编译 OpenWrt 固件 - P3TERX ZONE](https://p3terx.com/archives/build-openwrt-with-github-actions.html)

编译时空间不足问题：

[OpenWRT在Actions编译过程中因磁盘空间不足从而编译失败，如何清理磁盘空间？ · Issue #11159 · coolsnowwolf/lede · GitHub](https://github.com/coolsnowwolf/lede/issues/11159)

[减少编译所用空间 · Issue #7004 · coolsnowwolf/lede · GitHub](https://github.com/coolsnowwolf/lede/issues/7004)

缓存 cache 加速编译：

[使用github actions cache缓存工具链， 加速你的云编译项目，让你做更快的男人 by stupidloud · Pull Request #7796 · coolsnowwolf/lede · GitHub](https://github.com/coolsnowwolf/lede/pull/7796)

## 自定义编译

[GitHub - VIKINGYFY/OpenWRT-CI: 云编译OpenWRT固件](https://github.com/VIKINGYFY/OpenWRT-CI)

## 官方源

```shell
src/gz openwrt_koolshare_mod_core http://downloads.openwrt.org/snapshots/targets/x86/64/packages
src/gz openwrt_koolshare_mod_base http://downloads.openwrt.org/snapshots/packages/x86_64/base
src/gz openwrt_koolshare_mod_luci http://downloads.openwrt.org/snapshots/packages/x86_64/luci
src/gz openwrt_koolshare_mod_packages http://downloads.openwrt.org/snapshots/packages/x86_64/packages
src/gz openwrt_koolshare_mod_routing http://downloads.openwrt.org/snapshots/packages/x86_64/routing
src/gz openwrt_koolshare_mod_telephony http://downloads.openwrt.org/snapshots/packages/x86_64/telephony
```
