---

title: Linux iptables 命令笔记
date: 2022-12-25 22:51:48
updated: 2022-12-25 22:59:48
categories: Linux
tags: [Network, Linux]
description:
thumbnail: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/img/Iptables-banner.png
banner_img: https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/img/Iptables-banner.png

---

# Linux Iptables 命令笔记

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/img/Iptables-banner.png&webp=true)

## 认识

`iptables` 是运行在用户空间的应用软件，通过控制 Linux 内核 netfilter 模块，来管理网络数据包的处理和转发。

`iptables` 操作需要超级用户权限，以上命令通常只用于处理 IPv4 数据包；而对于 IPv6 数据包，则使用类似的 `ip6tables` 命令。

## 原理

iptables 的原理主要是对数据包的控制，看下图：

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/img/linux-iptables-1.png&webp=true)

1. 一个数据包进入网卡时，它首先进入 **PREROUTING(预路由)** 链，内核根据数据包目的 IP 判断是否需要转发出去.
2. 如果数据包就是进入本机的，它就会沿着图向下移动，到达 **INPUT** 链。数据包到了 INPUT 链后，任何进程都会收到它。本机上运行的程序可以发送数据包，这些数据包会经过 OUTPUT 链，然后到达 **POSTROUTING(路由后)** 链输出。
3. 如果数据包是要转发出去的，且内核允许转发，数据包就会如图所示向右移动，经过 FORWARD 链，然后到达 POSTROUTING 链输出。

## 规则 表 链

> iptables 由链组成,链中有规则

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/img/linux-iptables-2.png&webp=true)

### 规则 (rules)

规则（rules）其实就是网络管理员预定义的条件，规则一般的定义为“如果数据包头符合这样的条件，就这样处理这个数据包”。规则存储在内核空间的信息包过滤表中，这些规则分别指定了 **源地址、目的地址、传输协议**（如 TCP、UDP、ICMP）和服务类型（如 HTTP、FTP 和 SMTP）等。当数据包与规则匹配时，iptables 就根据规则所定义的方法来处理这些数据包，如放行（accept）、拒绝（reject）和丢弃（drop）等。配置防火墙的主要工作就是添加、修改和删除这些规则。

### 链 (chains)

链（chains）是数据包传播的路径，每一条链中可以有一条或数条规则。当一个数据包到达一个链时，iptables 就会从链中第一条规则开始检查，看该数据包是否满足规则所定义的条件。如果满足，系统就会根据该条规则所定义的方法处理该数据包；否则 iptables 将继续检查下一条规则，如果该数据包不符合链中任一条规则，iptables 就会根据该链预先定义的默认策略来处理数据包。

### 表 (tables)

表（tables）提供特定的功能，iptables 内置了 4 个表，即 raw 表、filter 表、nat 表和 mangle 表，分别用于实现包过滤，网络地址转换和包重构的功能。

其中 nat filter 表是常用的.

#### Filter 表

主要用于过滤数据包，主要利用在 filter 表中指定的规则来实现对数据包的过滤。**Filter 表是默认的表，如果没有指定哪个表，iptables 就默认使用 filter 表来执行所有命令**.

filter 表包含了 INPUT 链（处理进入的数据包），RORWARD 链（处理转发的数据包），OUTPUT 链（处理本地生成的数据包）在 filter 表中只能允许对数据包进行接受,丢弃的操作，而无法对数据包进行更改

#### Nat 表

**主要用于网络地址转换 NAT,用于端口和/或地址的转换.**

NAT 表包含了 PREROUTING 链（修改即将到来的数据包），POSTROUTING 链（修改即将出去的数据包），OUTPUT 链（修改路由之前本地生成的数据包）

## Iptables 命令

```shell
iptables [-t 表名] 命令选项 [链名] [条件匹配] [-j 目标动作或跳转] 
```

### 表名

表名：Filter, NAT, Mangle, Raw

起包过滤功能的为表 Filter，可以不填，不填默认为 Filter

### 命令选项

-A 在指定链的末尾添加（--append）一条新的规则

-D 删除（--delete）指定链中的某一条规则，按规则序号或内容确定要删除的规则

-I 在指定链中插入（--insert）一条新的规则，默认在链的开头插入

-R 修改、替换（--replace）指定链中的一条规则，按规则序号或内容确定

-L 列出（--list）指定链中的所有的规则进行查看，默认列出表中所有链的内容

-F 清空（--flush）指定链中的所有规则，默认清空表中所有链的内容

-N 新建（--new-chain）一条用户自己定义的规则链

-X 删除指定表中用户自定义的规则链（--delete-chain）

-P 设置指定链的默认策略（--policy）

-n 用数字形式（--numeric）显示输出结果，若显示主机的 IP 地址而不是主机名

-P 设置指定链的默认策略（--policy）

-v 查看规则列表时显示详细（--verbose）的信息

-V 查看 iptables 命令工具的版本（--Version）信息

-h 查看命令帮助信息（--help）

--line-number 查看规则列表时，同时显示规则在链中的顺序号

### 链名

可以根据数据流向来确定具体使用哪个链，在 Filter 中的使用情况如下：

```shell
INPUT链 – 处理来自外部的数据。 
OUTPUT链 – 处理向外发送的数据。 
FORWARD链 – 将数据转发到本机的其他网卡设备上。
```

### 条件匹配

-p 指定规则协议，如 tcp, udp,icmp 等，可以使用 all 来指定所有协议

-s 指定数据包的源地址参数，可以使 IP 地址、网络地址、主机名

-d 指定目的地址

-i 输入接口

-o 输出接口

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/img/linux-iptable-3.jpeg&webp=true)

![image](https://api.whaleluo.top/onedrive/file/?path=/picstorage/blog/img/linux-iptables-4.jpeg&webp=true)

### 目标值

数据包控制方式包括四种为：

1. ACCEPT：允许数据包通过。
2. DROP：直接丢弃数据包，不给出任何回应信息。
3. REJECT：拒绝数据包通过，必须时会给数据发送端一个响应信息。
4. LOG：在/var/log/messages 文件中记录日志信息，然后将数据包传递给下一条规则。
5. QUEUE：防火墙将数据包移交到用户空间
6. RETURN：防火墙停止执行当前链中的后续 Rules，并返回到调用链 (the calling chain)

## 应用

### NAT 转发

实现将本机（192.168.1.7:7410）端口流量转发给（192.168.1.160:9200）。

#### 1. 内核允许转发

```shell
vim /etc/sysctl.conf

# 添加如下代码：
net.ipv4.ip_forward=1

# 重启network服务
systemctl restart ****network

# 查看是否修改成功
sysctl net.ipv4.ip_forward
```

#### 2. 设置 Filter 表基础策略：允许入包/出包/转发

-P 设置链的基础策略

```shell
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
```

#### 3. 端口转发流量

建立一个双向映射

```shell
# 访问时转发
iptables -t nat -A PREROUTING -d 192.168.1.7/32 -p tcp --dport 7410 -j DNAT --to-destination 192.168.1.160:9200

# 接受来源于远程机器的数据包
# SNAT 地址伪装
iptables -t nat -A POSTROUTING -d 192.168.1.160/32 -p tcp --dport 9200 -j SNAT --to-source 192.168.1.7
```

```shell
sudo iptables -t nat -I PREROUTING -p tcp -d 10.10.40.40 --dport 3079 -j DNAT --to-destination 10.10.66.12:22
sudo iptables -t nat -I POSTROUTING -p tcp --dport 22 -d 10.10.66.12 -j SNAT --to-source 10.10.40.40
```

### 其他命令

显示 nat 规则列表：

```shell
iptables -t nat -L -n --line-numbers
```

删除指定规则：

```shell
iptables -t nat -D PREROUTING 1
```

允许出站的 DNS 连接:

```shell
iptables -A OUTPUT -p udp -o eth0 --dport 53 -j ACCEPT
iptables -A INPUT -p udp -i eth0 --sport 53 -j ACCEPT
```

将来自 422 端口的流量全部转到 22 端口:

这意味着我们既能通过 422 端口又能通过 22 端口进行 ssh 连接。启用 DNAT 转发。

```shell
iptables -t nat -A PREROUTING -p tcp -d 192.168.102.37 --dport 422 -j DNAT --to 192.168.102.37:22
```

除此之外，还需要允许连接到 422 端口的请求:

```shell
iptables -A INPUT -i eth0 -p tcp --dport 422 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 422 -m state --state ESTABLISHED -j ACCEPT
```

本机端口转发:

```shell
iptables -t nat -A PREROUTING -p tcp --dport 422 -j REDIRECT --to-ports 22
```

注意,在本机请求无法经过 PREROUTING 链,所以本机的 422 端口访问不了 22.

如果需要本机也可以访问，则需要配置 OUTPUT 链

特别注意: 本机访问外网的端口会转发到本地,导致访不到外网.实际上是访问到本地,建议不做 80 端口的转发或者指定目的 -d localhost：

```shell
iptables -t nat -A OUTPUT -d localhost -p tcp --dport 80 -j REDIRECT --to-ports 8080
```

原因：外网访问需要经过 PREROUTING 链，但是 localhost 不经过该链，因此需要用 OUTPUT。
