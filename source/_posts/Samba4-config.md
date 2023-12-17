---

title: Samba4 服务器配置最佳实践
date: 2022-12-25 19:37:48
updated: 2022-12-25 19:37:48
categories: Server
tags: [Server, Linux]
thumbnail: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/img/Smb-banner.png
banner_img: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/img/Smb-banner.png

---

# Samba4 服务器配置最佳实践

![banner](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/img/Smb-banner.png)

## 具体配置文件

**请按照实际情况进行修改!**

smb.conf.ini

```ini
[global]
   netbios name = CYX HOME
   #interfaces = |INTERFACES|
   server string = cyx
   #unix charset = |CHARSET|
   workgroup = WORKGROUP

   bind interfaces only = no

   max connections = 0

   deadtime = 15

   ## disable core dumps
   enable core files = no

   security = user

   
   #smb encrypt = default

   ## set invalid users
   ## 设置无效用户
   #invalid users = root

   ## map unknow users to guest
   ## 将未知用户映射为访客
   map to guest = Bad User
   #

   ## 允许客户端访问具有空密码的帐户。
   null passwords = yes

   passdb backend = smbpasswd

   ## Set location of smbpasswd ('smbd -b' will show default compiled location)
   ## 设置smbpasswd的位置（'smbd-b'将显示默认的编译位置）
   #smb passwd file = /etc/samba/smbpasswd 

   ## LAN (IPTOS_LOWDELAY TCP_NODELAY) WAN (IPTOS_THROUGHPUT) WiFi (SO_KEEPALIVE) try&error for buffer sizes (SO_RCVBUF=65536 SO_SNDBUF=65536)
   ## 用来设置服务器和客户端之间会话的Socket选项，可以优化传输速度
   #socket options = TCP_NODELAY SO_RCVBUF=8192 SO_SNDBUF=8192
   socket options = TCP_NODELAY IPTOS_LOWDELAY SO_RCVBUF=131072 SO_SNDBUF=131072
   #socket options = IPTOS_LOWDELAY TCP_NODELAY
   
   
   aio read size = 0
   #aio write size = 0

   ## 这可能会在没有 aio 支持的情况下加速客户端，但确实很危险，因为数据可能会丢失，文件可能会损坏。
   #aio write behind = /*.tmp/

   use sendfile = yes

   ## samba will behave as previous versions of Samba would and will fail the lock request immediately if the lock range cannot be obtained.
   ## samba 将像以前版本的 Samba 一样运行，如果无法获得锁定范围，它将立即使锁定请求失败。
   #blocking locks = No

   ## disable loading of all printcap printers by default (iprint, cups, lpstat)
   ## 默认禁用所有 printcap 打印机的加载（iprint、cups、lpstat）
   load printers = No
   printcap name = /dev/null

   ## Enabling this parameter will disable Samba's support for the SPOOLSS set of MS-RPC's.
   ## 启用此参数将禁用 Samba 对 MS-RPC 的 SPOOLSS 集的支持。
   disable spoolss = yes

   ## This parameters controls how printer status information is interpreted on your system.
   ## 此参数控制打印机状态信息在系统上的解释方式。
   ## (BSD, AIX, LPRNG, PLP, SYSV, HPUX, QNX, SOFTQ)
   printing = bsd

   ## Disable that nmbd is acting as a WINS server for unknow netbios names
   ## 禁用 nmbd 作为未知 netbios 名称的 WINS 服务器
   #dns proxy = No

   ## win/unix user mapping backend
   ## win/unix 用户映射后端
   #idmap config * : backend = tdb

   ## Allows the server name that is advertised through MDNS to be set to the hostname rather than the Samba NETBIOS name.
   ## 允许将通过 MDNS 通告的服务器名称设置为主机名，而不是 Samba NETBIOS 名称。
   ## This allows an administrator to make Samba registered MDNS records match the case of the hostname rather than being in all capitals.
   ## 这允许管理员使 Samba 注册的 MDNS 记录与主机名的大小写匹配，而不是全部大写。
   ## (netbios, mdns)
   #mdns name = mdns

   ## Clients that only support netbios won't be able to see your samba server when netbios support is disabled.
   ## 仅支持 netbios 的客户端在禁用 netbios 支持时将无法看到您的 samba 服务器。
   #disable netbios = Yes

   ## Setting this value to no will cause nmbd never to become a local master browser.
   ## 将此值设置为 no 将导致 nmbd 永远不会成为本地主浏览器。
   #local master = no

   ## (auto, yes) If this is set to yes, on startup, nmbd will force an election, and it will have a slight advantage in winning the election. It is recommended that this parameter is used in conjunction with domain master = yes, so that nmbd can guarantee becoming a domain master. 
   ##（自动，是）如果设置为是，在启动时，nmbd将强制进行选举，并且在赢得选举时会有一点优势。建议将此参数与domain master=yes结合使用，这样nmbd就可以保证成为域主机。
   #preferred master = yes

   ## (445 139) Specifies which ports the server should listen on for SMB traffic.
   ## (445 139) 指定服务器应在哪些端口上侦听 SMB 流量。
   ## 139 is netbios/nmbd
   smb ports = 445 139 4455 44555

   ## This is a list of files and directories that are neither visible nor accessible.
   ## 这是既不可见也不可访问的文件和目录的列表。
   ## Each entry in the list must be separated by a '/', which allows spaces to be included in the entry. '*' and '?' can be used to specify multiple files or directories as in DOS wildcards.
   ## 列表中的每个条目必须用“/”分隔，这允许在条目中包含空格。“*”和“？”可用于指定多个文件或目录，如在 DOS 通配符中一样。
   veto files = /Thumbs.db/.DS_Store/._.DS_Store/.apdisk/

   ## If a directory that is to be deleted contains nothing but veto files this deletion will fail unless you also set the delete veto files parameter to yes.
   ## 如果要删除的目录只包含否决文件，则此删除将失败，除非您还将删除否决文件参数设置为 yes。
   delete veto files = yes

################ Filesystem and creation rules ################
   ## reported filesystem type (NTFS,Samba,FAT)
   ## 报告的文件系统类型(NTFS、Samba、FAT)
   #fstype = FAT

   ## Allows a user who has write access to the file (by whatever means, including an ACL permission) to modify the permissions (including ACL) on it.
   ## 允许对文件具有写入访问权限(以任何方式，包括ACL权限)的用户修改其权限(包括ACL)。
   #dos filemode = Yes

   ## file/dir creating rules
   ## 文件/目录创建规则
   #create mask = 0666
   #directory mask = 0777
   #force group = root
   #force user = root
   #inherit owner = windows and unix
######### Dynamic written config options #########
   ## windows 7 能正常访问，而部分windows 10 不能访问，老是弹出“拒绝访问”，按网上方法设置了、系统重装了n遍还是一样，就用以下两行代码，能解决。
   lanman auth = yes
   ntlm auth = ntlmv1-permitted


#[sda1]
#   path = /mnt/sda1
#   #共享路径
#   force user = root
#   #强制用户
#   force group = root
#   #强制组
#   create mask = 0666
#   #创建文件自身权限
#   directory mask = 0777
#   #创建文件夹自身权限
#   read only = yes
#   write list = root
#   #对于其他用户只读(yes/no)
#   guest ok = no
#   #对于windows 以游客身份是否访问（yes/no），
#   inherit owner = yes
#   #目录继承
#   valid users = root you
#   #有效用户
#   invalid users = you
#   #无效用户


[mnt]
   path = /mnt/
   force user = root
   force group = root
   create mask = 0666
   directory mask = 0777
   read only = no
   guest ok = no
   inherit owner = yes
   valid users = root

[临时目录]
   path = /tmp
   force user = root
   force group = root
   create mask = 0777
   directory mask = 0777
   read only = no
   guest ok = no
   inherit owner = yes
```
