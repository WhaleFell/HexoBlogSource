---

title: 😜 树莓派折腾手册 (二)——手动搭建LNMP网站服务器环境 🙏
date: 2021-06-18 19:37:48
updated: 2021-06-18 19:37:48
categories: Linux
tags: [Respi, Linux]
description:
thumbnail: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619154345.jpg
banner_img: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619154345.jpg

---

# 😜 树莓派折腾手册 (二)——手动搭建 LNMP 网站服务器环境 🙏

> 前言：这个东西我折腾了很久，试过一键部署脚本: [https://lnmp.org/auto](https://lnmp.org/auto.html) 但是我想在局域网 +frp 穿透的外网，访问树莓派的网站，但是只能绑定一个域名，反正用多了就出各种问题，可能我不会用

```shell
sudo apt-get update   #好习惯，安装软件前先更新源列表 
```

## **1.安装 PHP7.3**

> **这里跳了很多坑，后来查文档才发现 Debian 10 buster 只能安装 PHP7.0 以上的版本**

```shell
sudo apt install -y -t buster php7.3-fpm php7.3-curl php7.3-gd php7.3-intl php7.3-mbstring php7.3-mysql php7.3-imap php7.3-opcache php7.3-sqlite3 php7.3-xml php7.3-xmlrpc php7.3-zip 
```

通过命令 `php -v` 能够看到 PHP 版本号 7.3，说明安装完成:

> 输出显示配置文件在/etc/php/7.3/cli/php.ini。注意，实际上配置文件有两个，另外一个在/etc/php/7.3/fpm/php.ini。通过命令行调用 php 时，会使用第一个配置文件；通过 fpm 调用 php（例如 nginx）会使用第二个配置文件。

php-fpm 常用管理命令:

```shell
开启php-fpm:
sudo systemctl start php7.3-fpm 
关闭php-fpm: 
sudo systemctl stop php7.3-fpm 
重启php-fpm: 
sudo systemctl restart php7.3-fpm 
编辑php-fpm配置文件:
sudo nano /etc/php/7.3/fpm/php.ini 
```

## **2.安装 nginx:**

```shell
sudo apt-get install nginx
```

> 安装完成后，会自动开启 nginx。在浏览器输入树莓派的 IP 地址，可以看到“Welcome to nginx!”

**nginx 常用管理命令：**

```shell
启动nginx: sudo systemctl start nginx 
关闭nginx：sudo systemctl stop nginx 
设置nginx开机启动：sudo systemctl enable nginx 
重启nginx：sudo systemctl restart nginx 
#配置文件的位置： 
默认的网站根目录：/var/www/html 
nginx配置文件目录：/etc/nginx/ 
nginx主配置文件位置：/etc/nginx/nginx.conf 
```

## 3.配置 Nginx 解析 Php (关键)

- 编辑配置 nginx 文件:

```shell
sudo nano /etc/nginx/sites-enabled/default 
```

找到 `# pass PHP scripts to FastCGI server` 后面的 `location` ，删除注释。修改后如下：

> PHP 的默认路径转发有问题导致的,因为很多 nginx 的默认 PHP 配置文件的写法为 **location ~ ​**​**php** 要改成 **location~.**​***php(***​***.***​**)*$**

```shell
index index.php index.html index.htm index.nginx-debian.html; 
location ~ .*\.php(\/.*)*$ { 
include snippets/fastcgi-php.conf; 
# 
# # With php-fpm (or other unix sockets): 
    fastcgi_pass unix:/run/php/php7.3-fpm.sock; 
# # With php-cgi (or other tcp sockets): 
#fastcgi_pass 127.0.0.1:9000; 
} 
```

- 保存后重启 nginx：

```shell
sudo systemctl restart nginx
```

**重启无报错则修改成功啦:**  
![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619152804.png&webp=true)

- 在网站根目录创建一个 php 文件：

```shell
sudo nano /var/www/html/index.php 
```

写入以下 php 代码并保存：

```shell
<?php phpinfo(); 
```

在浏览器中输入树莓派的 IP 地址即可看到 phpinfo:  
![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619152811.png&webp=true)

## **4.安装 mariaDB 数据库**

- 使用以下命令安装 `mariadb`:

```shell
sudo apt-get install mariadb-server mariadb-client 
```

- 执行数据库初始化安装:

```shell
sudo mysql_secure_installation 
```

> 根据提示设置数据库 `root用户密码`、`是否允许外网访问` 等，建议用**翻译软件**，一步步翻译。 `回车 n Y n Y Y`

- 尝试用**普通用户 pi**登录数据库:  
  `mysql -u root -p`  
  输入上一步设置的密码，发现无法登录，错误提示如下:

> ERROR 1698 (28000): Access denied for user ‘root’@’localhost’

原因: 数据库默认使用**特权用户 root 登录**，需要修改为**普通用户使用密码登录**

- 尝试用**特权用户 root**登录数据库:

```shell
sudo mysql -u root# 登入数据库后，依次执行以下SQL： use mysql;update user set plugin='mysql_native_password';flush privileges;exit;
```

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619152818.png&webp=true)

再次使用普通用户 pi `mysql -u root -p` 即可通过密码登录数据库，无需 root 权限执行:

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619152823.png&webp=true)

- 设置**数据库密码**  
  **依次执行以下 SQL：**

```sql
use mysql;   UPDATE user SET password=password('123456') WHERE user='root';   flush privileges;   exit; 
```

- 设置**mariaDB 数据库** ***外部网络访问权限***

> 根据官方的说法， MariaDB 为了**提高安全性**，默认只监听 127.0.0.1 中的 3306 端口并且禁止了远程的 TCP 链接，我们可以通过下面两步来开启**MySQL 的远程服务**

1. 打开文件 `sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf`，注释掉 bind-address 项，如下:  
   ![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619152829.png&webp=true)
2. 开启了**MySQL 监听远程连接**的选项，接下来需要给对应的**MySQL 账户分配权限**，允许使用该账户**远程连接**到 MySQL:  
   查看**用户账号信息**：

```sql
select User,host from mysql.user;
```

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619152834.png&webp=true)  
**root 账户**中的 host 项是**localhost**表示该账号只能进行**本地登录**，我们需要**修改权限**，执行 MySQL 命令:

```sql
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;FLUSH PRIVILEGES;exit;
```

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619152851.png&webp=true)

> 这个时候发现相比之前**多了一项**，它的**host 项是%**，这个时候说明配置成功了，我们可以用该账号进行**远程访问**了

- **mariadb 配置文件**保存在多个位置:

```shell
/etc/mysql/mariadb.cnf /etc/mysql/mariadb.conf.d/ /etc/mysql/conf.d/ 
```

- **mariadb 常用**命令:

```shell
#启动mariadb: sudo systemctl start mariadb #关闭/开启/重启 mariadb：systemctl stop/start/restart mariadb #设置mariadb开机启动：sudo systemctl enable mariadb 
```

- **连接 MySQL**数据库命令:

```shell
mysql -h主机地址 -u用户名 －p用户密码
```

> **(注:u 与 root 可以不用加空格，其它也一样)**

## 5.安装 `phpmyadmin` 可视化 MySQL 管理工具

> **官方网站**: [phpmyadmin官网](https://www.phpmyadmin.net/)
>
> - 用**wget**下载源码包包到 web 目录  
>   (请自行到官网获取最新下载链接)，截至 2020/8/4 版本为:  
>   **phpMyAdmin-5.0.2-all-languages.zip**

```shell
wget https://gproxy.cn/https://github.com/phpmyadmin/phpmyadmin/archive/RELEASE_5_0_4.zip
```

- **解压 zip 格式**源码包包到**web 目录**

```shell
sudo chmod 777 -R /var/www/html/unzip -d /var/www/html/ ~/phpMyAdmin-5.0.2-all-languages.zip 
```

> 若 unzip 不受支持请安装  
> **sudo apt-get install unzip**

- **重命名文件夹并修改参数**

```shell
cd /var/www/html/mv phpMyAdmin-5.0.2-all-languages phpmyadmincd phpmyadminmv config.sample.inc.php config.inc.phpnano config.inc.php$cfg['AllowArbitraryServer'] = true;
```

编辑 `config.inc.php` 文件，修改密钥字段:  
![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619152859.png&webp=true)

> 修改**blowfish_secret**字段，后面的密钥无仅仅用于加密而已，**尽量足够长**。**当然偷偷插入**​**~~喜欢的女孩子~~**​**名字也是可以的哦**

- 把**config.inc.php 文件权限修改**为 744

```shell
sudo chmod 744 config.inc.php
```

- 尝试访问 [http://你的树莓派ip/phpmyadmin](http://%E4%BD%A0%E7%9A%84%E6%A0%91%E8%8E%93%E6%B4%BEip/phpmyadmin)  
  ![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619152752.png&webp=true)  
  **启动高级功能** 会新建一个 phpmyadmin 数据库  
  ![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619152912.png&webp=true)  
  **安装成功！**  
  ![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619152920.png&webp=true)

## 6.搭建多个 `nginx` 虚拟主机

> 有时候我们要在主机的**不同端口**搭建不同用处的**web 服务**，这时就需要多个新建多个 nginx 虚拟主机的啦~

- 打开 `nginx` 主配置文件

  ```shell
  sudo nano /etc/nginx/nginx.conf
  ```

  ![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619152925.png&webp=true)

找到 `include` 字段就是每个**虚拟主机配置文件**，为了方便管理，我们在**用户家目录**新建一个 `nginx-conf` 存放我们的**nginx 虚拟主机文件**

```shell
# 在配置文件插入include /home/pi/nginx-conf/*;
```

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619152933.png&webp=true)

```shell
mkdir /home/pi/nginx-confnano /home/pi/nginx-conf/kodbox.conf
```

写入以下内容: ***(贴出一份完整的 nginx 虚拟主机配置，需要自行修改两个参数)***

```shell
# 监听端口 两个都要改server { listen 88 default_server; listen [::]:88 default_server; # SSL configuration # # listen 443 ssl default_server; # listen [::]:443 ssl default_server; # # include snippets/snakeoil.conf; root /home/pi/kodbox; #网站根目录位置 # Add index.php to the list if you are using PHP index index.php index.html index.htm index.nginx-debian.html; server_name _; location / {  try_files $uri $uri/ =404; } # pass PHP scripts to FastCGI server # location ~ .*\.php(\/.*)*$ {  include snippets/fastcgi-php.conf;  fastcgi_pass unix:/run/php/php7.3-fpm.sock; }}
```

重启 `nginx`，愿一切安好:

```shell
sudo systemctl restart nginxwget --content-disposition https://packagecloud.io/headmelted/codebuilds/packages/debian/stretch/code-oss_1.45.0-1586135971_arm64.deb/download.deb
```

## 7. 树莓派 `PHP` 调优

> #### **lnmp 默认环境**部署完成后，进行调优以应对**多并发，复杂任务的情景**

- 部署**PHP 探针**以测试

  > 部署服务器探针推荐 X 探针，GitHub 项目地址：
  >
  > [GitHub X刘海探针](https://github.com/kmvan/x-prober)
  >

```shell
#克隆项目到www目录cd /var/www/htmlwget https://github.com/kmvan/x-prober/raw/master/dist/prober.php#删除默认页rm index.phpmv prober.php index.phpsudo chmod 777 index.php
```

**尝试**访问 `http://树莓派ip` :

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619152940.png&webp=true)

> 可以看到这些参数都是好 **鸡肋的**

- 修改**php.ini** 仔细看好喽~

```shell
sudo nano /etc/php/7.3/fpm/php.ini
```

修改 ***post 方式提交的数据大小***，查找：`post_max_size` **酌情修改为 2000M**

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619152945.png&webp=true)

修改 ***运行超时秒数*** ，查找：`max_execution_time` **酌情修改为 3600s**

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619152950.png&webp=true)

修改 ***上传文件限制*** ，查找：`upload_max_filesize` **酌情修改为 2000M**

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619152956.png&webp=true)

修改 ***运行内存限制*** ，查找：`memory_limit` **酌情修改为 2000M**

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619153003.png&webp=true)

开启 ***文件上传*** ,查找：`file_uploads` 更改为 `On`

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619153007.png&webp=true)

- 更改完成后重启 `nginx+php-fpm`

  ```
  max_input_time = 3600;sudo systemctl restart nginxsudo systemctl restart php7.3-fpm
  ```

**更改后的探针页面**：

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619153012.png&webp=true)

## 8.搭建 `kodbox` 云私有云盘

> # 下载安装
>
> #### 即刻下载，开启私有云盘之旅
>
> **官网:**​****

- **下载源码**

```shell
cd ~mkdir kodboxcd kodboxwget http://static.kodcloud.com/update/download/kodbox.1.11.zipunzip kodbox.1.11.zipunzip kodbox.1.11.ziprm kodbox.1.11.zipchmod 777 ~/kodbox
```

尝试访问 **** :

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619153018.png&webp=true)

> 除了 PHP 版本外其他都可以通过哒~

- 安装系统缓存 `Redis`

  ```
  sudo apt-get install redis php-redis
  ```

  ![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619153025.png&webp=true)

**redis 服务会自动运行自动添加开机启动项，省心！！！**

**编辑**​`sudo nano /etc/php/7.3/fpm/php.ini` 文件加入：

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619153032.png&webp=true)

```
extension=redis.so#重启php-fpmsudo systemctl restart php7.3-fpm 
```

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/old/20210619153038.png&webp=true)

> #### 数据库选择**MySQL**，填入自己的密码
>
> #### 系统缓存类型选择 Redis
