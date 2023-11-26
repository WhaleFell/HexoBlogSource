---

title: Onedrive 完全使用指南
date: 2023-06-03 11:05:50
updated: 2023-06-03 11:05:50
categories: onedrive
tags: [onedrive, 云盘, Rclone]
description:
thumbnail:
banner_img:

---

# Onedrive 完全使用指南

## 前言

最近一直在寻找一个国内使用的高可用不限速的在线云盘存储，试过了很多国内的云盘存储，不是有内容审查就是网盘限速。这些网盘都拘泥于一个固定的客户端，不能做到 **多端可用、随地随用、增量同步。**

就选择了 Onedrive 这个云盘，在小咸鱼买了一年 39 CNY 的 office365 家庭版拼车。Onedirve 家庭版可以支持 6 个用户，每个用户有 1T 的 onedrive 空间和 5 台设备的 office365 授权。

Onedrive 支持将云空间挂载到各种软件上，例如：Rclone、Alist、oneindex。可以支持直链共享，目测在中国大陆的网速也不错。  
​![office-365-family](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/img/202307191615973.png)

## 创建 OneDrive API

### 获取 client_id

```text
5c160d06-e2cf-4dbf-a987-5b672cd6bb5ehyy
```

首先访问 [Microsoft Azure 应用注册](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)，登录账号后点击 **应用注册**

填写以下信息：

​![image](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/img/202307191615975.png)点击注册后可以看到你的应用的相关信息，复制好 应用程序 (客户端) ID ==，这个就是== client_id。

​![image](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/img/202307191615976.png)​

### 获取 client_secret

```text
dHd8Q~Nu8~IRN25ihNt3x-sIGrkE9n1tpFYGYbEohxy
```

依次点击 **证书和密码**，**新客户端密码**，在截止期限中将时间选择为最长（即两年）

​![image](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/img/202307191615977.png)​

然后就可以看见值和机密 ID，我们只需要记录下 **值** 就可以，这个就是 client_secret。

​![image](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/img/202307191615978.png)​

### 添加 API 权限

依次点击 **API 权限**，**添加权限**，**Microsoft Graph**，在右边栏搜索并添加权限。

需要 **Files.Read, Files.ReadWrite,Files.Read.All, Files.ReadWrite.All, offline_access, User.Read** 。

​​![image](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/img/202307191615979.png)​

添加以下权限：

​![image](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/img/202307191615980.png)​

### 添加身份验证

依次点击 **身份验证**，**添加平台**，**Web**

​![image](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/img/202307191615981.png)​在重定向 URI 中输入 ==http://localhost==

​![image](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/img/202307191615982.png)​至此，OneDrive API 创建完成

## 使用 Rclone

Rclone 是一个用于多个云平台之间同步文件和目录的命令行工具，其支持多种运营商网盘。使用 Golang 编写，**所以跨平台兼容性良好**。

支持 RC 接口可以通过 API 或 Web UI 操作。

* 官网网址：[https://rclone.org](https://www.moewah.com/go/aHR0cHM6Ly9yY2xvbmUub3Jn)
* 开源地址：[https://github.com/ncw/rclone](https://github.com/ncw/rclone)
* 中文文档部分翻译： [https://ld246.com/article/1600853705300](https://ld246.com/article/1600853705300)
* rclone 中文站点： [https://rclone.cn/](https://rclone.cn/)

### Docker

Rclone can serve a web based GUI. ​`rclone rcd --rc-web-gui`​​

53682 为 微软令牌验证端口

[https://rclone.org/onedrive/](https://rclone.org/onedrive/)

```shell
docker pull rclone/rclone:latest
```

```shell
docker run -d \
    -v /root/rclone:/config/rclone \
    -v /mnt/:/data \
    -p 5573:5572 \
    -p 53682:53682 \
    --name=rclone \
    --user $(id -u):$(id -g) \
    rclone/rclone \
    rcd --rc-web-gui \
    --rc-web-gui-force-update \
    --rc-web-gui-no-open-browser \
    --rc-addr 0.0.0.0:5572 \
    --rc-user admin \
    --rc-pass lovehyy9420
```

打开 http://IP:5573 并登录显示以下页面：

​![image](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/img/202307191615983.png)​

进入 docker bash：`docker exec -it rclone sh`​, 输入 rclone config ，按照提示进行 onedrive 设置。

远程 OA 验证：[https://rclone.org/remote_setup/](https://rclone.org/remote_setup/)

验证完毕后可以在 Explorer 查看文件：

​![image](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/img/202307191615985.png)### Rclone 常用命令

语法：

```shell
# 本地到网盘
rclone [功能选项] <本地路径> <网盘名称:路径> [参数] [参数] ...
# 网盘到本地
rclone [功能选项] <网盘名称:路径> <本地路径> [参数] [参数] ...
# 网盘到网盘
rclone [功能选项] <网盘名称:路径> <网盘名称:路径> [参数] [参数] ...
```

常用功能选项：

```text
rclone copy - 复制
rclone move - 移动，如果要在移动后删除空源目录，请加上 --delete-empty-src-dirs 参数
rclone sync - 同步：将源目录同步到目标目录，只更改目标目录。
rclone delete - 删除路径下的文件内容。
rclone purge - 删除路径及其所有文件内容。
rclone mkdir - 创建目录。
rclone rmdir - 删除目录。
rclone rmdirs - 删除指定灵境下的空目录。如果加上 --leave-root 参数，则不会删除根目录。
rclone check - 检查源和目的地址数据是否匹配。
rclone ls - 列出指定路径下的所有的文件以及文件大小和路径。
rclone lsl - 比上面多一个显示上传时间。
rclone lsd 列出指定路径下的目录
rclone lsf - 列出指定路径下的目录和文件
```

常用参数：

```text
-n = --dry-run - 测试运行，用来查看 rclone 在实际运行中会进行哪些操作。
-P = --progress - 显示实时传输进度。
--cache-chunk-size SizeSuffi - 块的大小，默认 5M，理论上是越大上传速度越快，同时占用内存也越多。如果设置得太大，可能会导致进程中断。
--cache-chunk-total-size SizeSuffix - 块可以在本地磁盘上占用的总大小，默认 10G。
--transfers=N - 并行文件数，默认为 4。在比较小的内存的 VPS 上建议调小这个参数，比如 128M 的小鸡上使用建议设置为 1。
--config string - 指定配置文件路径，string 为配置文件路径。比如在使用宝塔面板输入命令操作时可能会遇到找不到配置文件的问题，这时就需要指定配置文件路径。
```

### 授权保活

If you don't use rclone for 90 days the refresh token will expire. This will result in authorization problems. This is easy to fix by running the `rclone config reconnect remote:`​ command to get a new token and refresh token.

如果在 90 天内不使用 rclone，刷新令牌将过期。这将导致授权问题。通过运行 rclone config reconnect remote: 命令获取新令牌并刷新令牌，可以轻松解决此问题。

重新运行授权：

```shell
docker run --rm -it -v /root/rclone:/config/rclone -v /mnt/:/data --user $(id -u):$(id -g) rclone/rclone config reconnect onedrive:
```

### 命令示例

将 onedrive 上的全部内容下载到本地：

```shell
rclone copy source:sourcepath dest:destpsth

rclone copy onedrive:/ /data/sdb1/onedrive_old --transfers=8 -P
```

​![image](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/img/202307191615986.png)​​

速度还行，将旧的 onedrive 文件一同迁移到本地硬盘。

### 双向同步

```shell
rclone bisync remote1:path1 remote2:path2 [flags]


--check-access            确保在Path1和Path2文件系统上找到了预期的RCLONE_TEST文件，否则中止操作。
--check-filename string   用于--check-access的文件名（默认值：RCLONE_TEST）
--check-sync string       控制最终列表的比较：true|false|only（默认值：true）（默认值 "true"）
--filters-file string     从文件中读取过滤模式
--force                   跳过--max-delete安全检查并运行同步。考虑与--verbose一起使用
-h, --help                    bisync的帮助信息
--localtime               在列表中使用本地时间（默认值：UTC）
--no-cleanup              保留工作文件（用于故障排除和测试）。
--remove-empty-dirs       在最终清理步骤中删除空目录。
-1, --resync                  执行重新同步操作。Path1文件可能会覆盖Path2版本。首先考虑使用--verbose或--dry-run。
--workdir string          使用自定义的工作目录 - 用于测试。（默认值：$HOME/.cache/rclone/bisync）
```

```shell
rclone bisync /data/sdb1/Yellow/ onedrive:/Yellow/ --check-access --resync --cache-chunk-size 20M --drive-chunk-size 128M --transfers=8
```

仅同步备份到云盘，考虑到云盘备份不需要特别高的实时性，不需要双向同步，所以我选择通过 sync 将硬盘内容定时上传到云盘。[https://rclone.org/commands/rclone_sync/](https://rclone.org/commands/rclone_sync/)

**将源同步到目标，仅更改目标。**不传输源和目标上相同的文件，按大小和修改时间或 MD5SUM 进行测试。

目标更新以匹配源，**包括在必要时删除文件**（重复对象除外，见下文）。如果不想从目标位置删除文件，请改用复制命令。

```shell
rclone sync SOURCE remote:DESTINATION
```

```shell
rclone sync /data/sdb1/Yellow/ onedrive:/Yellow/ --cache-chunk-size 20M --transfers=8 -P
```

​![image](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/img/202307191615987.png)​

这是一个关于 `bisync`​ 命令的错误信息。该错误信息表明，在上一次运行 `bisync`​ 命令时出现了致命错误，导致无法找到先前的 `Path1`​ 或 `Path2`​ 列表。因此，`bisync`​ 命令无法确定哪些文件需要同步，因此它需要使用 `--resync`​ 选项来恢复同步操作。

如果您需要在生产环境中进行文件同步操作，建议使用其他可靠的工具，如 `rsync`​ 或 `robocopy`​。这些工具已经被广泛使用，并且已经被证明是可靠的文件同步工具。

```shell
2023/06/12 08:15:59 NOTICE: bisync is EXPERIMENTAL. Don't use in production!
2023/06/12 08:15:59 INFO : Synching Path1 "/data/sdb1/AMediaBack/" with Path2 "onedrive:AMediaBack/"
2023/06/12 08:15:59 ERROR : Bisync critical error: cannot find prior Path1 or Path2 listings, likely due to critical error on prior run
2023/06/12 08:15:59 ERROR : Bisync aborted. Must run --resync to recover.
```

### 主配置翻译

```shell
--ask-password 允许提示输入加密配置的密码（默认为true）
--auto-confirm 如果启用，不请求控制台确认
--backup-dir string 将备份制作成基于DIR的层次结构
--bind string 用于传出连接的本地地址，IPv4、IPv6或名称
--buffer-size SizeSuffix 每个--transfer读取文件时的内存缓冲区大小（默认为16Mi）
--bwlimit BwTimetable 带宽限制为KiB/s，或使用后缀B|K|M|G|T|P或完整时间表
--bwlimit-file BwTimetable 每个文件的带宽限制为KiB/s，或使用后缀B|K|M|G|T|P或完整时间表
--ca-cert stringArray 用于验证服务器的CA证书
--cache-dir string rclone将用于缓存的目录（默认为“$HOME/.cache/rclone”）
--check-first 在开始传输之前执行所有检查
--checkers int 并行运行的检查程序数（默认为8）
-c，--checksum 根据校验和（如果可用）和大小跳过，而不是修改时间和大小
--client-cert string 用于相互TLS身份验证的客户端SSL证书（PEM）
--client-key string 用于相互TLS身份验证的客户端SSL私钥（PEM）
--color string 何时显示颜色（和其他ANSI代码）AUTO|NEVER|ALWAYS（默认为“AUTO”）
--compare-dest stringArray 在比较过程中包括其他逗号分隔的服务器端路径
--config string 配置文件（默认为“$HOME/.config/rclone/rclone.conf”）
--contimeout Duration 连接超时（默认为1m0s）
--copy-dest stringArray 意味着--compare-dest，但也将文件从路径复制到目标
--cpuprofile string 将CPU配置文件写入文件
--cutoff-mode string 达到最大传输限制时停止传输的模式HARD|SOFT|CAUTIOUS（默认为“HARD”）
--delete-after 在同步时，在传输后删除目标上的文件（默认）
--delete-before 在同步时，在传输之前删除目标上的文件
--delete-during 在同步时，在传输期间删除文件
--delete-excluded 删除从同步中排除的目标上的文件
--disable string 禁用一系列功能的逗号分隔列表（使用--disable help查看列表）
--disable-http-keep-alives 禁用HTTP保持活动并仅使用每个连接一次。
--disable-http2 在全局传输中禁用HTTP/2
-n，--dry-run 进行试运行，没有永久更改
--dscp string 将DSCP值设置为连接，值或名称，例如CS1、LE、DF、AF21
--dump DumpFlags 要从中转储存的项目列表：头、主体、请求、响应、身份验证、过滤器、goroutines、openfiles
--dump-bodies 转储HTTP头和正文-可能包含敏感信息
--dump-headers 转储HTTP头-可能包含敏感信息
--error-on-no-transfer 如果没有传输文件，则设置退出代码9，在脚本中很有用
--exclude stringArray 排除与模式匹配的文件
--exclude-from stringArray 从文件中读取文件排除模式（使用-从标准输入读取）
--exclude-if-present stringArray 如果文件名存在，则排除目录
--expect-continue-timeout Duration：在使用 HTTP 的 expect / 100-continue 时超时的时间（默认为 1 秒）
--fast-list：如果可用，使用递归列表；使用更多的内存但更少的事务
--files-from stringArray：从文件中读取源文件名列表（使用 - 从标准输入读取）
--files-from-raw stringArray：从文件中读取源文件名列表，而不对行进行任何处理（使用 - 从标准输入读取）
-f, --filter stringArray：添加文件过滤规则
--filter-from stringArray：从文件中读取文件过滤模式（使用 - 从标准输入读取）
--fs-cache-expire-duration Duration：缓存远程文件的时间长度（0 表示禁用缓存）（默认为 5 分钟）
--fs-cache-expire-interval Duration：检查过期远程文件的间隔时间（默认为 1 分钟）
--header stringArray：为所有事务设置 HTTP 标头
--header-download stringArray：为下载事务设置 HTTP 标头
--header-upload stringArray：为上传事务设置 HTTP 标头
--human-readable：以人类可读的格式打印数字，大小带有后缀 Ki|Mi|Gi|Ti|Pi
--ignore-case：在过滤器中忽略大小写（不区分大小写）
--ignore-case-sync：同步时忽略大小写
--ignore-checksum：跳过校验和的后复制检查
--ignore-errors：即使存在 I/O 错误，也要删除
--ignore-existing：跳过所有已存在于目标位置的文件
--ignore-size：在跳过时忽略大小，使用修改时间或校验和
-I, --ignore-times：不跳过与大小和时间匹配的文件 - 传输所有文件
--immutable：不修改文件，如果已修改现有文件，则失败
--include stringArray：包括匹配模式的文件
--include-from stringArray：从文件中读取文件包含模式（使用 - 从标准输入读取）
-i, --interactive：启用交互模式
--kv-lock-time Duration：进程保持键值数据库锁定的最长时间（默认为 1 秒）
--log-file string：将所有内容记录到此文件
--log-format string：日志格式选项的逗号分隔列表（默认为“日期，时间”）
--log-level string：日志级别 DEBUG|INFO|NOTICE|ERROR（默认为“NOTICE”）
--log-systemd：激活日志记录器的 systemd 集成
--low-level-retries int：执行低级别重试的次数（默认为 10）
--max-age Duration：仅传输比此年龄年轻的文件，单位为 s 或后缀 ms|s|m|h|d|w|M|y（默认为关闭）
--max-backlog int：同步或检查后台日志中的最大对象数（默认为 10000）
--max-delete int：在同步时，限制删除的数量（默认为 -1）
--max-delete-size SizeSuffix：在同步时，限制删除的总大小（默认为关闭）
--max-depth int：如果设置，将递归深度限制为此（默认为 -1）
--max-duration Duration：rclone 将传输数据的最长时间（默认为 0 秒）
--max-size SizeSuffix：仅传输小于此大小的文件，单位为 KiB 或后缀 B|K|M|G|T|P（默认为关闭）
--max-stats-groups int：在 max 上，最大的统计组数将保留在内存中，最老的将被丢弃（默认为 1000）
--max-transfer SizeSuffix：要传输的数据的最大大小（默认为关闭）
--memprofile string：将内存配置文件写入文件
-M, --metadata：如果设置，复制对象时保留元数据
--metadata-exclude stringArray：排除与模式匹配的元数据
--metadata-exclude-from stringArray：从文件中读取元数据排除模式（使用 - 从标准输入读取）
--metadata-filter stringArray：添加元数据过滤规则
--metadata-filter-from stringArray：从文件中读取元数据过滤模式（使用 - 从标准输入读取）
--metadata-include stringArray：包括匹配模式的元数据
--metadata-include-from stringArray：从文件中读取元数据包含模式（使用 - 从标准输入读取）
--metadata-set stringArray：上传时添加元数据键=值
--min-age Duration：仅传输比此年龄更老的文件，单位为 s 或后缀 ms|s|m|h|d|w|M|y（默认为关闭）
--min-size SizeSuffix：仅传输大于此大小的文件，单位为 KiB 或后缀 B|K|M|G|T|P（默认为关闭）
--modify-window Duration：被认为是相同的最大时间差异（默认为 1 纳秒）
--multi-thread-cutoff SizeSuffix：对于文件大小超过此值的文件使用多线程下载（默认为 250Mi）
--multi-thread-streams int：用于多线程下载的最大流数（默认为 4）
--no-check-certificate：不验证服务器 SSL 证书（不安全）
--no-check-dest：不检查目标，无论如何都要复制
--no-console：隐藏控制台窗口（仅限 Windows）
--no-gzip-encoding：不设置 Accept-Encoding: gzip
--no-traverse：不在复制时遍历目标文件系统
--no-unicode-normalization：不规范化文件名中的 Unicode 字符
--no-update-modtime：如果文件相同，则不更新目标修改时间
--order-by string：如何排序传输的指令，例如“size，descending”
--password-command SpaceSepList        用于提供加密配置密码的命令
  -P, --progress                             在传输过程中显示进度
      --progress-terminal-title              在终端标题上显示进度（需要 -P/--progress）
  -q, --quiet                                尽可能少地打印信息
      --rc                                   启用远程控制服务器
      --rc-addr stringArray                  IP 地址:端口或 :端口，用于绑定服务器（默认 [localhost:5572]）
      --rc-allow-origin string               设置 CORS 的允许来源
      --rc-baseurl string                    URL 的前缀 - 留空表示根目录
      --rc-cert string                       TLS PEM 密钥（证书和 CA 证书的串联）
      --rc-client-ca string                  客户端证书颁发机构，用于验证客户端
      --rc-enable-metrics                    在 /metrics 上启用 Prometheus 指标
      --rc-files string                      要在 HTTP 服务器上提供的本地文件的路径
      --rc-htpasswd string                   htpasswd 文件 - 如果未提供，则不进行身份验证
      --rc-job-expire-duration Duration      过期完成的异步作业，时间超过此值（默认为 1m0s）
      --rc-job-expire-interval Duration      检查过期的异步作业的间隔时间（默认为 10s）
      --rc-key string                        TLS PEM 私钥
      --rc-max-header-bytes int              请求头的最大大小（默认为 4096）
      --rc-min-tls-version string            可接受的最低 TLS 版本（默认为 "tls1.0"）
      --rc-no-auth                           对某些方法不要求身份验证
      --rc-pass string                       用于身份验证的密码
      --rc-realm string                      用于身份验证的领域
      --rc-salt string                       密码哈希盐（默认为 "dlPL2MqE"）
      --rc-serve                             启用远程对象的服务
      --rc-server-read-timeout Duration      服务器读取数据的超时时间（默认为 1h0m0s）
      --rc-server-write-timeout Duration     服务器写入数据的超时时间（默认为 1h0m0s）
      --rc-template string                   用户指定的模板
      --rc-user string                       用于身份验证的用户名
      --rc-web-fetch-url string              获取 WebGUI 发布的 URL（默认为 "https://api.github.com/repos/rclone/rclone-webui-react/releases/latest"）
      --rc-web-gui                           在本地主机上启动 WebGUI
      --rc-web-gui-force-update              强制更新到 Web GUI 的最新版本
      --rc-web-gui-no-open-browser           不自动打开浏览器
      --rc-web-gui-update                    检查并更新到 Web GUI 的最新版本
      --refresh-times                        刷新远程文件的修改时间
      --retries int                          如果操作失败，重试此次数（默认为 3）
      --retries-sleep Duration               如果操作失败，重试操作之间的间隔时间，例如 500ms、60s、5m（0 表示禁用）（默认为 0s）
      --server-side-across-configs           允许服务器端操作（例如复制）跨不同的配置工作
      --size-only                            仅基于大小跳过，而不是基于修改时间或校验和
      --stats Duration                       打印统计信息的间隔时间，例如 500ms、60s、5m（0 表示禁用）（默认为 1m0s）
      --stats-file-name-length int           统计信息中文件名的最大长度（0 表示无限制）（默认为 45）
      --stats-log-level string               显示 --stats 输出的日志级别 DEBUG|INFO|NOTICE|ERROR（默认为 "INFO"）
      --stats-one-line                       使统计信息适合一行
      --stats-one-line-date                  启用 --stats-one-line 并添加当前日期/时间前缀
      --stats-one-line-date-format string    启用 --stats-one-line-date 并使用自定义格式化日期：将日期字符串括在双引号中，参见 https://golang.org/pkg/time/#Time.Format
      --stats-unit string                    在统计信息中以每秒字节或比特的形式显示数据速率（默认为 "bytes"）
      --streaming-upload-cutoff SizeSuffix   如果文件大小未知，则切换到分块上传的截止点，在达到截止点或文件结束时开始上传（默认为 100Ki）
      --suffix string                        添加到更改的文件后缀
      --suffix-keep-extension                在使用 --suffix 时保留扩展名
      --syslog                               使用 Syslog 记录日志
      --syslog-facility string               Syslog 的设施，例如 KERN、USER...（默认为 "DAEMON"）
      --temp-dir string                      rclone 将用于临时文件的目录（默认为 "/tmp"）
      --timeout Duration                     IO 空闲超时时间（默认为 5m0s）
      --tpslimit float                       每秒限制 HTTP 事务数
      --tpslimit-burst int                   --tpslimit 的最大事务数（默认为 1）
      --track-renames                        在同步时，跟踪文件重命名并在可能的情况下进行服务器端移动
      --track-renames-strategy string        使用 track-renames 时同步时使用的策略 hash|modtime|leaf（默认为 "hash"）
      --transfers int                        并行运行的文件传输数（默认为 4）
  -u, --update                               跳过在目标位置更新的较新文件
      --use-cookies                          启用会话 cookiejar
      --use-json-log                         使用 json 日志格式
      --use-mmap                             使用 mmap 分配器（请参阅文档）
      --use-server-modtime                   使用服务器修改时间而不是对象元数据
      --user-agent string                    将用户代理设置为指定的字符串（默认为 "rclone/v1.62.2"）
  -v, --verbose count                        打印更多的信息（重复以获得更多）
```

### 同步坚果云

因为坚果云 Webdav 有并发限制，所以需要限制线程数慢慢拉取。

将坚果云拉取到本地。

```shell
# rclone sync --interactive SOURCE remote:DESTINATION

docker run --rm \
-v /root/rclone:/config/rclone \
-v /mnt/:/data \
-v /root/rclone/bisync:/root/.cache/rclone/bisync \
--user $(id -u):$(id -g) \
rclone/rclone sync \
JGY:/ \
/data/sdb1/JianGuoYunBack/ \
--cache-chunk-size 20M \
--transfers=1 \
--verbose
```

### 在容器内定时运行同步命令

[https://meishizaolunzi.com/cron-in-docker/](https://meishizaolunzi.com/cron-in-docker/)

Alpine：已经预装，可以使用 `which crond`​ 查看。

对于 Docker 容器来说，多用户的作用不大，只需要专注全局的配置文件：`/etc/crontab`​ 即可。

```shell
vi /etc/crontab

* * * * * root date
```

### 通过 Crantab 实现定时备份

写一个备份脚本：rclone_bak.sh

实测 22000 个文件需要大概 6 分 35 秒 才能完全扫描并同步。

双向同步前需要先在 Onedrive 创建文件夹。

```shell
#!/bin/bash
# Website:  https://whalefall.top/

DATE=`date +%Y%m%d`

ARCHIVE_LOG_FILE=/root/backup.log

# Yellow 
docker run --rm -v /root/rclone:/config/rclone -v /mnt/:/data -v /root/rclone/bisync:/root/.cache/rclone/bisync --user $(id -u):$(id -g) rclone/rclone bisync /data/sdb1/Yellow/ onedrive:/Yellow/ --check-access --resync --workdir=/root/.cache/rclone/bisync --cache-chunk-size 20M --transfers=8 --verbose >> $ARCHIVE_LOG_FILE 2>&1

# AMediaBack
docker run --rm -v /root/rclone:/config/rclone -v /mnt/:/data -v /root/rclone/bisync:/root/.cache/rclone/bisync --user $(id -u):$(id -g) rclone/rclone bisync /data/sdb1/AMediaBack/ onedrive:/AMediaBack/ --check-access --resync --workdir=/root/.cache/rclone/bisync --cache-chunk-size 20M --transfers=8 --verbose >> $ARCHIVE_LOG_FILE 2>&1

# Music
docker run --rm -v /root/rclone:/config/rclone -v /mnt/:/data -v /root/rclone/bisync:/root/.cache/rclone/bisync --user $(id -u):$(id -g) rclone/rclone bisync /data/sdb1/Music/ onedrive:/Music/ --check-access --resync --workdir=/root/.cache/rclone/bisync --cache-chunk-size 20M --transfers=8 --verbose >> $ARCHIVE_LOG_FILE 2>&1

# Output log file
echo -e "\e[1;32m---------------------------------------------\e[0m" >> $ARCHIVE_LOG_FILE
echo -e "每日备份 Backup date: "$(date +%Y-%m-%d)"\t""Finish time: "$(date +%H:%M:%S) >> $ARCHIVE_LOG_FILE
echo -e "\e[1;32m---------------------------------------------\e[0m\n" >> $ARCHIVE_LOG_FILE
```

快速备份文件夹，双向同步。[https://rclone.org/commands/rclone_bisync/](https://rclone.org/commands/rclone_bisync/)

> This will effectively make both Path1 and Path2 filesystems contain a matching superset of all files. Path2 files that do not exist in Path1 will be copied to Path1, and the process will then sync the Path1 tree to Path2.

```shell
#!/bin/bash
# Website:  https://whalefall.top/

DATE=`date +%Y%m%d`

ARCHIVE_LOG_FILE=/root/backup_fast.log

# onedrive_fast
docker run --rm -v /root/rclone:/config/rclone -v /mnt/:/data -v /root/rclone/bisync:/root/.cache/rclone/bisync --user $(id -u):$(id -g) rclone/rclone bisync /data/sdb1/onedrive_fast/ onedrive:/onedrive_fast/ --check-access --resync --workdir=/root/.cache/rclone/bisync --cache-chunk-size 20M --transfers=8 >> $ARCHIVE_LOG_FILE 2>&1

# Output log file
echo -e "\e[1;32m---------------------------------------------\e[0m" >> $ARCHIVE_LOG_FILE
echo -e "快速备份 Backup date: "$(date +%Y-%m-%d)"\t""Finish time: "$(date +%H:%M:%S) >> $ARCHIVE_LOG_FILE
echo -e "\e[1;32m---------------------------------------------\e[0m\n" >> $ARCHIVE_LOG_FILE
```

### 简单同步

将源同步到目标，只修改目标。  

>参考：[performance - rclone to OneDrive is excruciatingly slow - Super User](https://superuser.com/questions/1729603/rclone-to-onedrive-is-excruciatingly-slow)  
>`--drive-chunk-size 128M` 增加上传文件分块的大小。

```shell
docker run --rm \
-v /root/rclone:/config/rclone \
-v /mnt/:/data \
-v /root/rclone/bisync:/root/.cache/rclone/bisync \
--user $(id -u):$(id -g) \
rclone/rclone sync \
/data/sdb1/AMediaBack/ \
onedrive:/AMediaBack/ \
--cache-chunk-size 20M \
--drive-chunk-size 128M \
--transfers=8 \
--verbose
```

pastes：

```shell
docker run --rm \
-v /root/rclone:/config/rclone \
-v /mnt/:/data \
-v /root/rclone/bisync:/root/.cache/rclone/bisync \
--user $(id -u):$(id -g) \
rclone/rclone sync \
/data/sdb1/WinEXE/ \
onedrive:/WinEXE/ \
--cache-chunk-size 20M \
--transfers=8 \
--verbose

# 神秘学资料

docker run --rm \
-v /root/rclone:/config/rclone \
-v /mnt/:/data \
-v /root/rclone/bisync:/root/.cache/rclone/bisync \
--user $(id -u):$(id -g) \
rclone/rclone sync \
/data/sdb1/神秘学资料/ \
onedrive:/神秘学资料/ \
--cache-chunk-size 20M \
--transfers=8 \
--verbose

# HamRadio

docker run --rm \
-v /root/rclone:/config/rclone \
-v /mnt/:/data \
-v /root/rclone/bisync:/root/.cache/rclone/bisync \
--user $(id -u):$(id -g) \
rclone/rclone sync \
/data/sdb1/HamRadio/ \
onedrive:/HamRadio/ \
--cache-chunk-size 20M \
--transfers=8 \
--verbose

# 双向同步 AMediaBack
$ docker run --rm \
-v /root/rclone:/config/rclone \
-v /mnt/:/data \
-v /root/rclone/bisync:/root/.cache/rclone/bisync \
--user $(id -u):$(id -g) \
rclone/rclone bisync \
/data/sdb1/AMediaBack/ \
onedrive:/AMediaBack/ \
--cache-chunk-size 20M \
--transfers=8 \
--localtime \
--verbose --resync --check-access


rclone --config="/root/rclone/rclone.conf" \
--checkers=16 --cache-dir="/root/rclone/" \
--transfers=10 --verbose --cache-chunk-size=20M \
rclone bisync --config="/root/rclone/rclone.conf" \
--checkers=80 --cache-dir="/root/rclone/" \
--transfers=10 --verbose --cache-chunk-size=20M --check-first --stats=1s --checksum --retries 3 --low-level-retries 10 \
/mnt/sdb1/Onedrive-Sync \
onedrive: \
--resync --check-access --dry-run -vvv --localtime --timeout=10s 


# rclone browser generate command
D:\rclone-v1.62.2-windows-amd64\rclone.exe copy --ignore-existing --verbose --transfers 2 --checkers 80 --contimeout 60s --timeout 300s --retries 3 --low-level-retries 10 --stats 1s --stats-file-name-length 0 --fast-list jgy:我的坚果云 E:\jgy\我的坚果云\

```

### 查看 Rclone 进程

```shell
ps -ef | grep rclone
13307 root      727m S    docker run --rm -v /root/rclone:/config/rclone -v /mnt/:/data -v /root/rclone/bisync:/root/.
13611 root      745m S    rclone sync JGY:/ /data/sdb1/JianGuoYunBack/ --cache-chunk-size 20M --transfers=1 --verbose
14867 root      727m S    docker run --rm -v /root/rclone:/config/rclone -v /mnt/:/data -v /root/rclone/bisync:/root/.
15207 root     1134m S    rclone sync /data/sdb1/WinEXE/ onedrive:/WinEXE/ --cache-chunk-size 20M --transfers=8 --verb
15651 root      726m S    docker run --rm -v /root/rclone:/config/rclone -v /mnt/:/data -v /root/rclone/bisync:/root/.
16003 root      939m S    rclone sync /data/sdb1/神秘学资料/ onedrive:/神秘学资料/ --cache-chunk-size 20M --
17596 root      1124 S    grep rclone
```

### RcloneNg UI

[https://github.com/ElonH/RcloneNg](https://github.com/ElonH/RcloneNg)

## Windows Mount

使用 Rclone 挂载 Onedrive 盘。

> official docs： [https://rclone.org/commands/rclone_mount/](https://rclone.org/commands/rclone_mount/)  
> fix [OneDrive mount - extremely slow](https://forum.rclone.org/t/onedrive-mount-extremely-slow/28267)

```shell
rclone mount onedrive: q: --network-mode --cache-dir E:\onedrive --volname=rclone_od --vfs-cache-mode=full --vfs-cache-max-size=20G --vfs-read-chunk-size-limit=256M --user-agent=Chrome --no-checksum --no-modtime --drive-chunk-size=256M
```

* `--allow-other`：指的是允许非当前 Rclone 用户外的用户进行访问
* `--attr-timeout 5m`：文件属性缓存，（大小，修改时间等）的时间。如果小鸡配置比较低，建议适当提高这个值，避免过多的和内核交互，占用资源。
* `-vfs-cache-mode full`：开启 VFS 文件缓存，这样可减少 Rclone 与 API 交互，同时可提高文件读写效率
* `--vfs-cache-max-age 24h`：VFS 文件缓存时间，这里设置的 24 小时，如果文件很少更改，建议设置更长的时间
* `--vfs-cache-max-size 10G`：VFS 文件缓存上限大小，建议不超过当前空余磁盘的 50%
* `vfs-read-chunk-size-limit 100M`：分块读取大小，这里设置的是 100M，可提高文件读的效率，比如 1G 的文件，大致分为 10 个块进行读取，但与此同时 API 请求次数也会增多
* `--buffer-size 100M`：内存缓存，如果您内存比较小，可降低此值，如果内存比较大，可适当提高
* `--daemon`：指后台方式运行
* `--drive-chunk-size 128M`：增加上传文件分块的大小。

## Onedrive Index 在线列出网盘

[https://github.com/spencerwooo/onedrive-vercel-index](https://github.com/spencerwooo/onedrive-vercel-index)

最大的优点是 Server-less (free) 使用 vercel 免费部署并支持绑定自定义域名。

Quick Start：[https://ovi.swo.moe/docs/getting-started](https://ovi.swo.moe/docs/getting-started)

申请个人 API：[https://ovi.swo.moe/docs/advanced#using-your-own-clientid-and-clientsecret-1](https://ovi.swo.moe/docs/advanced#using-your-own-clientid-and-clientsecret-1)

### 解决部署一周后出现的 404 错误

今天是 2023/6/6 日，上去云盘看突然显示部署错误了：

​![image](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/img/202307191615988.png)​

经过 Github Issue 查找，[#772](https://github.com/spencerwooo/onedrive-vercel-index/discussions/722) 也同样存在这个问题，解决方法：

> 如果一进入首页就显示这个提示的话，请检查下最近是否修改过 `Microsoft`​ 的账户密码。如有修改请删除 `upstash`​ 中的 redis 缓存，再刷新你的网站重新进行认证。
>
> 進入  
> [https://console.upstash.com/](https://console.upstash.com/)  
> 首頁會看到  
> Databases  
> [onedrive-vercel-index](https://console.upstash.com/redis/)
>
> 點進去後點上方的 Data Browser  
> 把裡面的 token 都刪除  
> 就可以重新認證了

一查是因为 redis 在线网站限额。改用 [https://app.redislabs.com/](https://app.redislabs.com/)

discuss 当站点访问量过大时： [https://github.com/spencerwooo/onedrive-vercel-index/discussions/595](https://github.com/spencerwooo/onedrive-vercel-index/discussions/595) 使用：[https://redis.com/try-free/](https://redis.com/try-free/)

## OneManager Serverless

[OneManager Serverless](https://github.com/qkqpttgf/OneManager-php/blob/master/readme_cn.md)

## 使用 Alist

文档：[https://alist-doc.nn.ci/docs/driver/onedrive/](https://alist-doc.nn.ci/docs/driver/onedrive/)

## Use Proxy

### Windows

```shell
set http_proxy=socks5://127.0.0.1:10808
set https_proxy=$http_proxy
set HTTP_PROXY=$http_proxy
set HTTPS_PROXY=$http_proxy

D:\rclone-v1.62.2-windows-amd64\rclone.exe copy --verbose --transfers 16 --checkers 8 --contimeout 60s --timeout 300s --retries 3 --low-level-retries 10 --stats 1s --stats-file-name-length 0 --fast-list Z:\sdb1\2022.11.26白纸革命 onedrive:政治/2022.11.26白纸革命
```
