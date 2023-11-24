---
title: WFWRT 系统使用说明
date: 2023-11-24 23:19:72
updated: 2023-11-24 23:19:72
categories:
  - OpenWRT
tags:
  - Linux
  - OpenWRT
  - Network
  - NAS
description: 
thumbnail: 
banner_img:
---

# WFWRT 系统使用说明

Dear Always, 感谢您购买并使用这个 WFWRT 系统，本系统基于 **OpenWRT LEDE** 构建，加入了本人很多自定义的元素，**优化使软路由处于最佳的工作状态**，希望您使用愉快。

为了保留您折腾 **OpenWRT** 的好奇心和兴趣，受限于边幅限制，instrument 中并不会说的那么详细，还请移步到各各项目的官方文档、GitHub 获得进一步的指示。

## Initialization 初始化设置

原先路由器的配置：**务必关掉 DHCP 服务**，打开 AP 模式（如果有），**网关和 DNS 全部设置成 192.168.8.1** 各品牌大同小异，并修**改路由器 IP** `192.168.8.254` 防止和其他 equipment clash。如果有 多路由组成的 **mesh** 可以尝试将另一条路由器和接软路由的路由器相连。

将光猫设置成拨号模式，然后准备两根网线，一根 **将光猫与软路由 WAN 口连接**，一根 **将软路由的 LAN 与 原先的路由器 LAN 口连接**。

配置：

1. 先用网线和软路由 LAN 口连接，理论上 PC 会自动获取网段为 `192.168.8.0/24` 的 IP 地址，用浏览器输入 [http://192.168.8.1/](http://192.168.8.1/) 进入 WFWRT 管理后台，默认账号密码 `root/loveroot`。
2. 在 **网络 -> 接口 -> WAN -> 修改** 设置您的 PPPOE 拨号账号密码，保存修改并等待连接，访问国内网站测试是否可以正常上网。（**其他选项已经是最优解，请不要乱摸摸**）
3. 测试公网 IP 的连通性，在接口 WAN 里面可以看到外网 IP，尝试访问 IP:9000 查看是否可达 Docker Container 界面。

完成上述配置，软路由就基本配置完成了。

## Service List 服务列表

1. SSH 地址 192.168.8.1:22 root/loveroot
2. **DDNS**-GO 域名解析 192.168.8.1:9876 root/loveAlways 请自行设置 API 等信息
3. 内网测试：192.168.8.1:6688
4. V2ray 回家协议，用于回到家中的内网，请自行在 Client 设置分流。

   ```shell
   vmess+WS 协议 ID 5dde3c45-8884-46b2-b0e6-8b04cdc6a578 AlterID:64 
   监听端口 10000  Path: /wss/
   ```

5. qBittorrent 种子下载器

   > WARNING: 默认下载的位置是 `/mnt/disk/downloads` 请将硬盘挂载好在下载！不然会撑爆内存！

   ​	<http://192.168.8.1:8600/> 账号密码：admin/adminadmin

   配置已经是最好的，不要乱摸摸。

6. Aira2 下载器

   WebUI：192.168.8.1:6880

   去到设置页面，Aria2 RPC 地址填写 192.168.8.1:6800 密码：loveAlways

7. Alist 文件管理

   > 默认管理路径 `/mnt/` 下面的文件，请将硬盘挂载到该**路径的任意子目录**

   <http://192.168.8.1:5244/> admin/admin

   Alist 还只带了一个 Webdav 服务，请自行观摩 Official Docutment 自行摸索。

8. 网络服务器 WebServer

   本系统有一个运行在 8080 端口上的 nginx ，配置目录在 `/root/nginx/conf` ，静态文件目录 `/root/nginx/www`，操作方法与一般的 Nginx 无异，请自行操作！

   另外，PHP 环境目前还没有部署，目前是纯静态服务器，不过也很简单。

## 科学上网

推荐使用 Clash 小猫咪上网，我的高速节点已经提供在 OpenClash 的 Subsribe URL 上面，请尽情享用！

直接打开 Clash 并更新订阅即可使用，不用修改任何配置，已经修改的最好了。默认使用 Fake-IP DNS TUN 模式，最好的模式，可有效防止 DNS 劫持等问题。 

## 端口映射

网络 -> socat 设置，**功能比家用路由器更强大！**

## 系统恢复/重装

TODO

如果系统真的**病入膏肓**了，可以去系统页面重置，因为 OpenWRT 使用 **Square File System**，**所有我部署的东西将全部神隐！请谨慎操作！只为了保留您最基本的上网服务。**

## 硬盘挂载

主板上有 2 个 SATA 接口，和一个电源。

请自行摸索，**系统 -> 硬盘管理**，如果是 3.5 寸的机械硬盘请使用外部供电，避免主板供电出现问题，如需要，请使用 USB 硬盘转接盒，主板**全部 USB 都是 3.0 配置**。

理论上两块硬盘配置好 Raid 后，就可以直接挂载成为 Raid0 ，请自行探索。
