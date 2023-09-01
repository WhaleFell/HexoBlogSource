---

title: Linux 运维笔记(持续更新)
date: 2022-03-20 16:37:48
updated: 2022-03-20 16:37:48
categories: Linux
tags: [Linux]
description:
thumbnail: http://oss.whaleluo.top/blog/img/Linux-Operation-Note-Banner.png-picsmall
banner_img: http://oss.whaleluo.top/blog/img/Linux-Operation-Note-Banner.png-picsmall

---

# Linux Operation Note

​![Banner](http://oss.whaleluo.top/blog/img/Linux-Operation-Note-Banner.png-picsmall)​

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

### FRP

#### 服务端配置

frps.ini

```ini
[common]
bind_addr = 0.0.0.0
bind_port = 57700
bind_udp_port= 57711

token = lovehyy

dashboard_port = 57710
dashboard_user = admin
dashboard_pwd = lovehyy

max_pool_count = 9999
max_ports_per_client = 0

tls_only = true
allow_ports = 57700-57800
```

#### 客户端

frpc.ini

```ini
[common]
server_addr = vps.lskyl.xyz
server_port = 57700
token = lovehyy
user = dlgz-nas
login_fail_exit = false
protocol = tcp
tcp_mux = true
dns_server = 223.5.5.5
tls_enable = true

[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 22
remote_port = 57722
use_encryption = false
use_compression = true
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

编辑 /etc/ssh/sshd_config 文件，进行如下设置：

```shell
RSAAuthentication yes
PubkeyAuthentication yes
```

重启 ssh 服务：

```shell
service sshd restart
```

### ZSH 终端美化

安装

```shell
cd ~
sudo apt-get install git zsh wget
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

[Docker](https://hub.docker.com/r/ilemonrain/html5-speedtest/)

```shell
sudo docker run --restart=always \
--name openspeedtest \
-d -p 6688:80 ilemonrain/html5-speedtest
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
> * ​`<目录路径>`​：指定要共享的目录的路径。可以是绝对路径或相对路径。
> * ​`<允许访问的客户端>`​：指定被允许访问该共享目录的客户端。可以使用 IP 地址、域名或子网掩码来指定。也可以使用通配符进行模式匹配，如 `*`​表示允许所有客户端访问。
> * ​`(选项)`​：可选项，用于指定共享选项。常用的选项包括：
>
>   * ​`rw`​：允许读写访问权限。
>   * ​`ro`​：只允许读取访问权限。
>   * ​`noaccess`​：禁止访问该共享目录。
>   * ​`root_squash`​：将客户端的 root 用户映射为匿名用户（通常是 nfsnobody），以增加安全性。
>   * ​`all_squash`​：将所有用户映射为匿名用户，用于增加安全性。
>   * ​`async`​：异步写入，提高性能但降低可靠性。
>   * ​`sync`​：同步写入，确保数据的可靠性但性能较低。
>   * ​`insecure`​：允许不安全的访问请求，如非特权端口。
>   * ​`no_subtree_check`​：不进行子树检查。
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
docker run -d --name ddns-go --restart=always --net=host -v /root/ddns-go:/root jeessy/ddns-go
```

## Nginx

docker running

```sh
docker run -d -p 8080:8080 \
--name nginx-proxy \
--restart=always \
-v /root/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
-v /root/nginx/www:/usr/share/nginx/html \
-v /root/nginx/cert:/usr/local/nginx/cert \
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
docker exec acme.sh --set-default-ca --server letsencrypt --issue --dns dns_cf -d *.whaleluo.top -d www.whaleluo.top

# 导出 nginx 使用的证书
docker exec acme.sh --install-cert -d whaleluo.top --key-file /acme.sh/private.key --fullchain-file /acme.sh/cert.pem

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

## 引用 Reference

> 感谢以下博客的作者和教程网站，如有侵权请及时联系博主删除。

1. Golang 和 Linux 学习笔记合集 [https://www.huweihuang.com/](https://www.huweihuang.com/)

‍
