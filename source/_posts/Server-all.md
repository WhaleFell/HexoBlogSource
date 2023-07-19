---

title: 服务器备忘录✨
date: 2022-03-20 19:37:48
updated: 2022-03-20 19:37:48
categories: Server
tags: [Server, Linux]
thumbnail: https://images.samsung.com/is/image/samsung/p5/semiconductor/solutions/applications/server/3-kf-energy-efficiency-mobile.jpg?$ORIGIN_JPG$
banner_img: https://images.samsung.com/is/image/samsung/p5/semiconductor/solutions/applications/server/3-kf-energy-efficiency-mobile.jpg?$ORIGIN_JPG$

---

# 服务器备忘录

> **2022 年 3 月 19 日** Update.

# 落落家 X86

## 服务器配置

- **运行内存 (RAM)：**4GB（笔记本 ddr3 内存条）
- **CPU：**4 x Intel(R) Atom(TM) CPU D2550 @ 1.86GHz / 512 KB cache [英特尔凌动® 处理器 D2550 (intel.cn)](https://www.intel.cn/content/www/cn/zh/products/sku/65470/intel-atom-processor-d2550-1m-cache-1-86-ghz/specifications.html) 2 核 4 线程
- **服务器系统**：Ubuntu 18.04 Server

  Linux whalefall 4.15.0-171-generic #180-Ubuntu SMP Wed Mar 2 17:25:05 UTC 2022 x86_64
- **硬盘：**​`df -h`

  ```shell
  Filesystem      Size  Used Avail Use% Mounted on
  /dev/sdb2       146G   18G  122G  13% /  # 系统盘 150GB
  /dev/sda1       299G  294G  5.0G  99% /mnt/disk  # 3.5寸硬盘1 300GB (跑wx)
  /dev/sdc1       466G  129G  338G  28% /mnt/mainDisk  # 2.5寸硬盘2 500GB (NAS)
  ```

- **网络：** `ifconfig`

  ```shell
  enp2s0:  # 主网卡 
          inet 192.168.5.123  netmask 255.255.255.0  broadcast 192.168.5.255
  enp1s0:  # 副网卡
          inet 192.168.5.124  netmask 255.255.255.0  broadcast 192.168.5.255
  ```

  - enp2s0：主网卡，跑 **qBittorrent**
  - enp1s0：副网卡，docker network —— macnet （openwrt），网心云 docker

## 运行服务

### Nginx

> 配置文件：`/etc/nginx/conf.d`; `/etc/nginx/sites-enabled`
>
> 默认配置：[Nginx默认配置](#Nginx-default-config)

1. Port: 80

   Desc: 运行探针服务。

   Path: `/var/www/html`
2. Port: 86

   Desc: python3-cookbook 3.0.0 文档。

   Path: `/www/openvpn_admin/`
3. Port: 88

   Desc: 可道云 Kodcloud.

   Path: `/www/kodcloud`
4. Port: 89

   Desc: AiraNg 下载器。（已反向代理 Aira2 6800）[配置文件](#AiraNg-download-config)

   Path: `/www/kodcloud`

### Docker

1. Port：9090

   Desc：Portainer web admin

   command：[source](#run-portainer-command)
2. Port：8989

   Desc：qBittorrent download

   command：[source](#run-qbittorrent-command)
3. Port：18888

   Desc：网心云（容器魔方）

   command：[source](#run-wxedge-command)
4. network：macnet

   ip：192.168.5.66

   Desc：openwrt 软路由

   command：[source](#run-openwrt-by-docker)

# 部分配置文件

## Nginx_default_config

```shell
server {
        listen 80 default_server;
        listen [::]:80 default_server;
        root /www/;
        index index.php index.html index.htm index.nginx-debian.html;
        server_name _;
        location / {
                try_files $uri $uri/ =404;
        }
        location ~ .*\.php(\/.*)*$ {
                include snippets/fastcgi-php.conf;
                # With php-fpm (or other unix sockets):
                fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
                # With php-cgi (or other tcp sockets):
        #       fastcgi_pass 127.0.0.1:9000;
        }
}
```

## AiraNg_download_config

```
server {
        listen 89 default_server;
        listen [::]:89 default_server;
        root /www/AiraNg/;
        index index.php index.html index.htm index.nginx-debian.html;
        server_name _;
        location / {
                try_files $uri $uri/ =404;
        }
        location ~ .*\.php(\/.*)*$ {
                include snippets/fastcgi-php.conf;
                # With php-fpm (or other unix sockets):
                fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
                # With php-cgi (or other tcp sockets):
        #       fastcgi_pass 127.0.0.1:9000;
        }
        # 反向代理 Aria ws
        location /jsonrpc {
        proxy_redirect off;
        proxy_pass http://127.0.0.1:6800/jsonrpc;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
}
```

### Run qBittorrent Command

```shell
# 运行 qbittorrent docker 版
 docker create \
    --name=qbittorrent \
    -e QB_WEBUI_PORT=8989 \
    -e QB_EE_BIN=false \
    -e UID=0 \
    -e GID=0 \
    -e QB_TRACKERS_UPDATE_AUTO=true \
    -e QB_TRACKERS_LIST_URL=https://trackerslist.com/all.txt \
    -e TZ=Asia/Shanghai \
    -e UMASK=022 \
    -p 6881:6881 \
    -p 6881:6881/udp \
    -p 8989:8989 \
    -v /root/qb_config:/config \
    -v /mnt/mainDisk/download/:/Downloads \
    --restart unless-stopped \
    johngong/qbittorrent:latest
```

### run_portainer_command

```shell
# 运行汉化版 Portainer
docker run -d -p 9000:9000 
--name portainer \ 
--restart always \ 
-v /var/run/docker.sock:/var/run/docker.sock \ 
-v portainer_data:/data \ 
-v /root/public:/public \ 
portainer/portainer
```

### run_wxedge_command

```shell
docker run -d --name=wxedge --restart=always --privileged --net=host  --tmpfs /run --tmpfs /tmp -v /mnt/disk/wxedge_storage:/storage:rw -e NIC=enp2s0  onething1/wxedge
```

> -e NIC：指定网卡

### run_openwrt_by_docker

```shell
# 新建一个网络 指定网卡
docker network create -d macvlan --subnet=192.168.5.0/24 --gateway=192.168.5.1 -o parent=enp2s0 macnet

# 运行 openwrt 镜像 指定ip
docker run --restart always --name openwrt -d --network macnet --ip=192.168.5.66 --privileged openwrt_myself:latest

# 进入容器命令行，修改 /etc/network/config
config interface 'loopback'
        option proto 'static'
        option ipaddr '127.0.0.1'
        option netmask '255.0.0.0'
        option device 'lo'

config interface 'lan'
        option type 'bridge'
        option ifname 'eth0'
        option proto 'static'
        option netmask '255.255.255.0'
        option gateway '192.168.5.1'
        option dns '192.168.5.1'
        option ipaddr '192.168.5.66'
        
# openwrt 防火墙自定义规则添加
iptables -t nat -I POSTROUTING -o eth0 -jMASQUERADE
```

### run_ttnode_by_docker

```shell
sudo docker run -d \
  -v /tiptime_dir:/mnt/data/ttnode \
  -v /var/run/docker.sock:/var/run/docker.sock \
  --name ttnode \
  --hostname ttnode \
  --net=host \
  --restart=always \
  tiptime/ttnode:latest
```
