---
title: Linux 运维笔记(持续更新)
date: 2022-03-20 16:37:48
updated: 2022-03-20 16:37:48
categories: Linux
tags: [Linux]
description: 
thumbnail: http://oss.whaleluo.top//blog/img/Linux-Operation-Note-Banner.png-picsmall
banner_img: http://oss.whaleluo.top//blog/img/Linux-Operation-Note-Banner.png-picsmall
---
# Linux Operation Note

![Banner](http://oss.whaleluo.top//blog/img/Linux-Operation-Note-Banner.png-picsmall)

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

```shell
sudo docker run --restart=unless-stopped \
--name openspeedtest \
-d -p 3000:3000 \
-p 3001:3001 openspeedtest/latest
```

## Nginx

docker running

```sh
docker run -d -p 8080:80 \
--name nginx-proxy \
--restart=always \
-v /root/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
-v /root/nginx/www:/usr/share/nginx/html \
-v /root/nginx/cert:/usr/local/nginx/cert \
nginx:latest
```

### 调优

在 server 字段下添加

```conf
client_max_body_size 5G;
client_header_timeout 1m;
client_body_timeout 1m;
proxy_connect_timeout 60s;
proxy_read_timeout 1m;
proxy_send_timeout 1m;        
```

### 反向代理

```conf
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

反向代理 ws.[nginx docs](http://nginx.org/en/docs/http/websocket.html)

```conf
location /chat/ {
    proxy_pass http://backend;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
}
```

自动添加 `/` 在 server 字段下添加:

```conf
server_name_in_redirect off;
```

### 添加证书

```conf
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

        ssl                      off;
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

```conf
add_header Access-Control-Allow-Origin *;
add_header Access-Control-Allow-Methods GET,POST,OPTIONS;
add_header Access-Control-Allow-Headers Content-Type,Authorization;
```

### 自用完整的 nginx.conf

```conf
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
        listen 8080 ssl http2;
        server_name _;

        ssl                      off;
        ssl_certificate     /usr/local/nginx/cert/cert.pem;
        ssl_certificate_key  /usr/local/nginx/cert/private.key;

        ssl_session_timeout  5m;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_prefer_server_ciphers on;

        error_page 497 https://$host:8080$request_uri;
        
        client_max_body_size 5G;
        client_header_timeout    1m;
        client_body_timeout      1m;
        proxy_connect_timeout     60s;
        proxy_read_timeout      1m;
        proxy_send_timeout      1m;        

        location / {
                root   /usr/share/nginx/html;
                index  index.html index.htm;
        }

        location /sdr/ {
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header Range $http_range;
            proxy_set_header If-Range $http_if_range;

            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            
            proxy_redirect off;
            proxy_http_version 1.1;
            proxy_pass http://192.168.8.10:8073/;
        }

        location /wss/ {
            proxy_redirect off;
            proxy_pass http://192.168.8.1:10000/wss/;
            proxy_http_version 1.1;

            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";

            proxy_set_header Host $http_host;

            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
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
            proxy_pass http://192.168.8.1:8989/;
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
