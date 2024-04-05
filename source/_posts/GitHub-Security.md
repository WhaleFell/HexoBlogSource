---
title: GIT 安全使用指南
date: 2024-04-03 16:35:83
updated: 2024-04-03 16:35:83
categories:
    - Git
tags:
    - GitHub
    - Cybersecurity
    - OpenSourceSecurity
    - 开源安全
    - 网络安全
description:
thumbnail:
banner_img:
---

# Git 安全使用指南

每时每刻，网络上成千上万的机器人贪婪的检索 GitHub，这包括开源软件 Attacker, Hacker ,甚至于受国家政府支持的 Attacker。

## 概述

### 开源供应链攻击（Open source supply chain attack）

近年来，**供应链攻击事件** 不断频发。过去一年中，恶意软件包上传到各种公共包管理库的事件数量呈爆炸式增长，这表明攻击者越来越青睐这种初始访问策略。根据软件供应链管理公司 Sonatype 的数据，在各个开源生态系统中检测到的恶意软件包数量同比增长了两倍。

开源软件供应链指的是广泛使用的第三方库、框架、组件等。例如 Python 的 fastapi、flask、requests 等，JavaScript 的 vue3、express、axios、lodash 等，Kubernetes，Docker，等服务工具。这些都是网络基础设施的重要组成部分。

幸运的是，这些网络基础设施都是 Open Source 的，任何人都能对其代码进行 review。但黑客也会利用这一点，用自己的 contributor 身份，抑或是盗取具有 contributor permission 账号的 cookie，巧妙地向开源软件下毒，这种下毒方式短时间内不会立即发现，随着软件依赖库的更新被分发到世界各地的互联网关键设施，黑客就能进出自由的控制政府、企业、个人的网络设备。

例如开发者可以通过修改 Python 的 dependency 的 requirements.txt ，当使用者下载并运行时，攻击开始了。

```shell
aiohttp>=3.6.0,<3.9.0
# 恶意软件包 透过冒充的域名 pythonhosted.org 的网站下载
https://files.pythonhosted.org/packages/18/93/1f005bbe044471a0444a82cdd7356f5120b9cf94fe2c50c0cdbf28f1258b/aiohttp-3.9.3.tar.gz
```

所以作为开发者，我们需要时刻保持警惕，不要随意下载未知的依赖库，尽可能使用官方源进行下载。有条件就进行 code review，或者使用一些安全工具进行扫描。

### 开源隐私泄露（Open source privacy leak）

每天数以百万计的机器人在 GitHub 上检索代码，匹配代码中的敏感信息，各种 Key/Session/Token/password/数据库密码/CardID/CreditCard 等。很多开发者贪图方便，开发时将这些敏感信息硬编码在代码中，这样一来，如果开源到 GitHub 上，就自我开盒了。

有证据表明，中国政府会重点关注敏感项目(如FQ、Politics等)的 contributor 甚至 star ，尝试通过 profile、commit、issue、email、social media 精准定位墙内信息，精准喝茶。

解决方法也很简单：

1. 打开 GitHub 的 Code security and analysis（代码安全和分析）-> Push protection for yourself (保护自己的推送)，这样就可以 Block commits that contain supported secrets across all public repositories on GitHub. （阻止包含支持的秘密的提交）
2. 不要偷懒，将敏感信息存储在环境变量中，或者使用专门的配置文件进行管理。并加入到 .gitignore 文件中，**不要上传到 GitHub 上**。
3. 敏感的 repository 设置为 **private**，不要公开。
4. 使用 GitHub 提供的 Keep my email addresses private (保持我的邮箱私密)，透过私密邮箱进行 commit。
5. GitHub 开启 Two-Factor Authentication(2FA 双因素验证)，增加账号安全性。

## Action 行动

### Initiation [/ɪˌnɪʃ.iˈeɪ.ʃən/] 初始化

配置全局用户名和邮箱

```shell
# 配置全局用户名和邮箱
git config --global user.name "whalefell"
git config --global user.email "whalefall@unimelb.edu.au"
# 查看配置
git config --list
```

ssh-keygen 使用 **RSA算法** 生成 SSH key ，并将公钥添加到 GitHub 上。  
可以选择输入 `passphrase` 更加安全。
位置：公钥 `~/.ssh/id_rsa.pub` 私钥 `~/.ssh/id_rsa`  
妥善保管上面两个文件，一同放在 KeePass 中。  

```shell
ssh-keygen -t rsa -C "youremail@example.com"
# verify
ssh -T git@github.com
```

创建 repository

```shell
git init .
git add .
git commit -m "initiation"
```

### Git Proxy

由于高墙的存在, 需要使用代理进行访问. I know this because it's necessary for Chinese guys, lol.

```shell
# set http proxy but it only working in http(s) git protocol
git config --global http.proxy http://127.0.0.1:10809
git config --global https.proxy https://127.0.0.1:10809

# clear proxy
git config --global --unset http.proxy
git config --global --unset https.proxy

# SSH proxy
# edit `~/.ssh/config`
# socks5
Host *
    ProxyCommand connect -S {proxyserver}:{port} %h %p

# http
Host *
    ProxyCommand connect -H {proxyserver}:{port} %h %p

# windows
Host *
ProxyCommand "D:\Git\mingw64\bin\connect.exe" -S 127.0.0.1:10808 %h %p

```

解决了一个迷思: 为什么 HTTP 代理能够代理很多非 HTTP 协议的请求?

ref: [stackoverflow: why-are-http-proxies-able-to-support-protocols-like-irc-and-ftp](https://stackoverflow.com/questions/12026247/why-are-http-proxies-able-to-support-protocols-like-irc-and-ftp)

> Q: Why are HTTP proxies able to support protocols like socks and FTP?
>
> A: HTTP proxy is able to support high level protocols other than HTTP,Because it supports CONNECT method,  
> HTTP 代理能够支持除了HTTP以外很多高级别的协议, 因为它支持 **CONNECT** 方法.  
>
> The CONNECT method is a way **to tunnel any kind of connection** through an HTTP proxy. By default, the proxy establishes a TCP connection to the specified server, responds with an HTTP 200 (Connection Established) response, and then shovels packets back and forth between the client and the server, **without understanding or interpreting the tunnelled traffic**
> CONNECT 方法是一种通过HTTP代理隧道化任何连接的方法. 代理默认建立一个到指定服务器的TCP连接, 响应一个HTTP 200 (Connection Established) 响应, 然后在客户端和服务器之间来回传输数据包, 而不理解或解释隧道流量.  
>
> 简单理解, HTTP 代理通过 CONNECT 方法将 TCP 的数据打包转发给目标服务器, 然后将数据包原封不动的返回给客户端, 从而实现了对非 HTTP 协议的支持. HTTP 代理支持所有基于 TCP 的协议, 但是不支持 UDP 协议.

### Use SSH over HTTPS prot

Use SSH over HTTPS prot: 通过 443 端口使用 SSH 协议，避免被 Firewall 阻挡。

```shell
# verify
ssh -T -p 443 git@ssh.github.com
git clone ssh://git@ssh.github.com:443/YOUR-USERNAME/YOUR-REPOSITORY.git
```

### Verify commit signature

使用 GPG 验证并签名你的提交，您可以在本地签名提交。这些标签或提交在 GitHub 上标记为 **已验证的绿色小勾 Verified**，因此其他人可以确信更改来自 **可信来源**。有关 PGP 的使用见下一 part。

```shell
gpg --list-keys  # 列出所有密钥

# 公钥特征，包括了密钥的参数
# 加密算法是 rsa，长度为 2048，生成于 2019-08-04，用途是 Signing 和 Certificating，一年之后过期以及密钥的 ID。
pub   rsa2048 2019-08-04 [SC] [expires: 2021-08-03]
      1BA074F113915706D141348CDC3DB5873563E6B2
# 生成密钥时所输入的个人信息。
uid           [ultimate] fortest <test@test.com>
# 子密钥特征，格式和公钥部分大致相同
sub   rsa2048 2019-08-04 [E] [expires: 2021-08-03]

# 根据密钥 ID 导出对应的 GPG 公钥字符串
gpg --armor --export {key_id}
```

然后，在 Github 的 SSH and GPG keys 中，新增一个 GPG key，内容即是上述命令的输出结果。

再次提醒，GPG 密钥中个人信息的邮箱部分，必须使用在 Github 中验证过的邮箱，否则添加 GPG key 会提示未经验证。

配置 Private email：

```shell
gpg --list-secret-keys --keyid-format=long
gpg --edit-key { sub_key_id }
adduid # 添加一个新的邮箱
save
# 导出字符串并添加到 GitHub
gpg --armor --export { sub_key_id }
```

利用 GPG 私钥对 Git commit 进行签名:

```shell
# 设置签名所用的 GPG 密钥 ID
git config --global user.signingkey {key_id}
# 在每次 commit 的时候，加上-S参数，表示这次提交需要用 GPG 密钥进行签名
git commit -S -m "..."
# 如果觉得每次都需要手动加上-S有些麻烦，可以设置 Git 为每次 commit 自动要求签名：
git config --global commit.gpgsign true
```

信任 GitHub 的 GPG 密钥：虽然所有的 commit 在 Github 中查看都是 **Verified**，但是有一些比较特殊：在 Github 网页端进行的操作，比如创建仓库。这些 commit 并没有用我们之前生成的密钥进行签名，而是由 Github 代为签名了。这样的结果就是，我们本地无法确认这些签名的真实性。

```shell
# import GitHub GPG key
curl https://github.com/web-flow.gpg | gpg --import
# sign and trust GitHub GPG key
gpg --sign-key 4AEE18F83AFDEB23
```

这时候，每一次 commit 都要输入 passphrase，可以使用 `gpg-agent` 来缓存密码，避免重复输入。

`~/.gnupg/gpg-agent.conf`:

```shell
default-cache-ttl 3600
```

## PGP 使用

> 世界上有两种密码:一种是防止你的小妹妹偷看你的文件;另一种是防止当局阅读你的文件.
> —— Bruce Schneier《应用密码学》

`GNU Privacy Guard`（GnuPG 或 GPG）是一个密码学软件，用于**加密、签名**通信内容及**管理 非对称 密码学的密钥**。

非对称密码大家应该接触过，这项技术在许多领域都有适用。这种算法需要两个 **钥匙**，一个是可以公开的 **公钥** ，对应的另一个就是应保管好的 **私钥** 。**用公钥加密的内容只能使用私钥解开，而使用私钥加密的内容也只能使用公钥解开。**

目前计算机难以高效地进行**质因数分解**，比如我们将 2147483647 乘以 998244353 利用计算机很容易得到结果 2143713423777595391 ，但是反过来就扔给你一个 2143713423777595391 很难反推出原来两个质数。

利用这个特性，加上一个合适的流程，我们就得到了一个加密手段。

如果假象我们在使用一个去中心化通讯平台，每个人有唯一的一个**账号（公钥）和密码（私钥）**，我要私聊一个朋友，内容肯定不想让别人知道，但是在去中心化网络中难以避免经过别人转手，这时候我就可以**拿那位朋友的账号（公钥）加密**，这样只有那位**朋友使用自己的私钥**才能解开这些内容；同样的，他怎么确定信息是我发出的而不是别人**伪造我的身份**发出的呢？那我在**用他的公钥加密后再使用自己的私钥加密**（也叫 Signature），他使用**我的公钥发现能解开**，那就必是我发出的无疑了。（但是你的私钥泄露了就……）

PGP能保证 一条信息是你相信的人发的，除了你俩之外别人无法解密， 而且这条消息在传送时中间没有经过任何哪怕是一个标点一个字节的修改。

名词解释：

```text
A    =>    Authentication
C    =>    Certify
E    =>    Encrypt
S    =>    Sign
?    =>    Unknown capability
sec  =>    Secret Key
ssb  =>    Secret SuBkey
pub  =>    Public Key
sub  =>    Public Subkey
```

### 生成密钥

PGP 的密钥分为两种：**主密钥** 和 **子密钥**，主密钥用于签名和认证，子密钥用于加密。只需要主密钥的公钥就行，包含了子密钥的信息。

生成主密钥：

```shell
# step 0 
# 这里不推荐使用的 `gpg --gen-key`
gpg --full-gen-key

# step 1
#  默认就可以
Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
  (14) Existing key from card
Your selection?

# step 2
# 此处输入你希望的密钥长度， RSA的不应低于2048 bits，当然输入的数字越大越安全，相应的，加解密的速度也会更慢
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (4096)


# step 3
# 默认可以选0 ，即永不过期， 这里我选了2y，  因为到期之前随时可以更改你的过期时间，以确保你对此密钥仍拥有控制权
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0)  2y


# step 4
# 注意了： 这里的邮箱， 如果你不打算使用PGP为你的Git记录认证， 这里其实是可以随便输入的，不需要是你的邮箱， 甚至不需要是一个真实存在的邮箱，只要接受你信息的人知道就行。隐私泄漏问题很严重，你一旦设置了，并且发布到公钥服务器，就永远删不掉了 😅

GnuPG needs to construct a user ID to identify your key.
Real name:  linus   # 这里名字可以是网名，可以是任意名字，如果你注重隐私就不要输入自己真名了 
Email address: linus@outlook.com  
Comment:     # 备注可以留空

# step 6
# 确认无误后输入 o
You selected this USER-ID:
    "linus <linust@outlook.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? o

# step 7
# 输入一个复杂的密码 并确认
┌──────────────────────────────────────────────────────┐
│ Please enter the passphrase to                       │
│ protect your new key                                 │ 
│                                                      │
│ Passphrase: │________________________________________│
│                                                      │
│       <OK>                              <Cancel>     │
└──────────────────────────────────────────────────────┘

# step 8
# 随机移动你的鼠标，越随机你的密钥越安全
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.


# step 9 大功告成
gpg: key 99F583599B7E31F1 marked as ultimately trusted
gpg: revocation certificate stored as '/root/.gnupg/openpgp-revocs.d/705358AB85366CAB05C0220F99F583599B7E31F1.rev'
public and secret key created and signed.

pub   rsa3072 2021-01-11 [SC]
      705358AB85366CAB05C0220F99F583599B7E31F1    # 你的 key id
uid                      linus <linus@outlook.com>
sub   rsa3072 2021-01-11 [E]    # 这个是自动生成的用于加密的子密钥，E代表Encrypt 加密
```

生成子密钥：你日常使用应该使用子密钥，**主密钥除了签发新的子密钥** 不要使用。建议为不同环境，不同用途都单独生成子密钥，互不干扰。

```shell
# step 0
gpg --edit-key linus # 或者key id  

# step 1  进入gpg交互界面
gpg (GnuPG) 2.2.20; Copyright (C) 2020 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

sec  rsa3072/99F583599B7E31F1
     created: 2021-01-11  expires: never       usage: SC
     trust: ultimate      validity: ultimate
ssb  rsa3072/6FE9C71CFED44076
     created: 2021-01-11  expires: never       usage: E
[ultimate] (1). linus <linus@outlook.com>C

# step 2  
gpg>   addkey
Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
  (14) Existing key from card
Your selection? 4   
# 根据你的用途选择， 这里生成一个只用于签名的子密钥（sign only）

#  后面的选择和主密钥生成的大同小异，按提示操作即可

# 生成完毕后
sec  rsa3072/99F583599B7E31F1
     created: 2021-01-11  expires: never       usage: SC
     trust: ultimate      validity: ultimate
ssb  rsa3072/6FE9C71CFED44076
     created: 2021-01-11  expires: never       usage: E
ssb  rsa3072/FDB960B857D397F6
     created: 2021-01-11  expires: never       usage: S
[ultimate] (1). linus <linus@outlook.com>

#  last step
gpg>  save  #  记得save， 直接退出的话什么也没有
```

生成撤销证书 revoke certification

假如你忘了主密钥的密码，或者丢失了对主密钥的控制权（丢失，被夺取），如果没有 **撤销凭证** 的话， 除了一个个通知你的朋友们没有任何办法 证明你不再使用这个密钥，这简直是灾难。

生成的 `revoke.pgp` 就是撤销凭证， 有了这个撤销凭证，你可以在没有密码的情况下 **使一个公钥失效**，所以一定要妥善保存，而且最好比主密钥多一份。

```shell
# step 0
# 按提示走完流程就可以
gpg --gen-revoke -ao revoke.pgp linus # uid 或者key id

# step 1
sec  rsa3072/99F583599B7E31F1 2021-01-11 linus <linus@outlook.com>

Create a revocation certificate for this key? (y/N) y
Please select the reason for the revocation:
  0 = No reason specified
  1 = Key has been compromised # 密钥被泄露
  2 = Key is superseded
  3 = Key is no longer used
  Q = Cancel
(Probably you want to select 1 here) 3
```

由于 PGP 没有提供任何 **将吊销信息通知其他用户的方式**，他不能保证没人会使用撤销了的已经变得不安全的密钥。

你丢失的私钥仍然可以被攻击者使用，并用来解密那些没有更新你的公钥的人发送的加密消息。 revoke 子密钥并更新公钥后，若有人用老的公钥加密信息，虽然你仍然可以解密，但是攻击者同样可以，这时候是极度不安全的。

例如：如果A的私人密钥被盗，她将发出一个 **密钥撤销证书**（key revocation certificate），但是由于这个密钥的分发是非正式的且将费大量的时间和口舌，故不能保证密钥环中每一个有 A 公开密钥的用户都能收到。

所以在你将密钥**撤销**后，请将发布到你一贯公布公钥的地方， 并尽可能通知其他人。

撤销主密钥：

```shell
gpg --import gpg-linus.asc                                               # 在一台新的电脑上导入你的公钥
gpg: key 99F583599B7E31F1: "linus <linus@outlook.com>" not changed
gpg: Total number processed: 1
gpg:              unchanged: 1

gpg --import revoke  # 导入你备份的撤销凭证，直接会导致密钥不可用
# detail
gpg: key 99F583599B7E31F1: "linus <linus@outlook.com>" revocation certificate imported
gpg: Total number processed: 1
gpg:    new key revocations: 1
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   1  signed:   1  trust: 0-, 0q, 0n, 0m, 0f, 1u
gpg: depth: 1  valid:   1  signed:   0  trust: 1-, 0q, 0n, 0m, 0f, 0u
gpg: next trustdb check due at 2021-09-29

gpg -k                 # 查看密钥，已经revoke
# detail
pub   rsa3072 2021-01-11 [SC] [revoked: 2021-01-11]
      705358AB85366CAB05C0220F99F583599B7E31F1
uid           [revoked] linus <linus@outlook.com>
```

撤销子密钥：

```shell
gpg --edit-key linus  
  
gpg >   list  # 列出你所有的子密钥
gpg >   key  {n}  # 选择你要销毁的子密钥的 序号
gpg >   revkey
gpg >   save    # 退出前一定要save, 不然所有更改不会生效
```

由于吊销前进行加密的内容仍需被吊销的密钥进行解密，所以被吊销的密钥也**不应丢弃**。

此外，若将 PGP 用于 SSH 认证，由于SSH的私钥认证并不通过主密钥来认证，所以即使吊销私钥，也可以进行访问，所以吊销后应当修改使用该密钥的SSH服务端来避免非法访问。

### 管理密钥

```shell
# 列出所有公钥、子公钥
gpg --list-keys 
# 列出所有密钥、子密钥
gpg --list-secret-keys 

# 简化
gpg -k 
gpg -K

# 这样并没有列出子密钥的id, 而且没有打印出指纹信息， 是不安全的。所以在你查看密钥时应该
gpg --fingerprint -K --keyid-format long
 
 # 输出
sec   rsa3072/0x99F583599B7E31F1 2021-01-11 [SC]  # 长ID
      Key fingerprint = 7053 58AB 8536 6CAB 05C0  220F 99F5 8359 9B7E 31F1 #指纹信息
uid                   [ultimate] linus <linus@outlook.com>
ssb   rsa3072/0x6FE9C71CFED44076 2021-01-11 [E]            # 斜杠后面的就是子密钥ID
ssb   rsa3072/0xFDB960B857D397F6 2021-01-11 [S]
```

### 备份密钥 backup certification

```shell
gpg --list-secret-keys --keyid-format=long
# 备份步骤
# step 1: 导出秘钥信息，其中包括所有公钥，私钥，信任网数据库文件，进行备份
gpg --export > public-keys.gpg
gpg --export-secret-keys > private-keys.gpg
gpg --export-ownertrust > ownertrust.asc

# step 2: 导出秘钥吊销凭证，建议单独备份
gpg --armor --gen-revoke [primary key ID] > revocation.asc

# 恢复秘钥
# step 1: 导出公钥，私钥和信任网数据库
gpg --import public-keys.gpg
gpg --import private-keys.gpg
gpg --import-ownertrust ownertrust.asc

# 字符串文件备份
gpg -ao public-key --export linus   # 导出公钥
gpg  -ao secret-key --export-secret-key 99F583599B7E31F1!    # 导出主私钥，建议secret-key 替换为你的加密设备备份文件的路径，直接导入到设备中
gpg  -ao sign-subkey --export-secret-subkeys FDB960B857D397F6!     #导出有[S]标识、签名用子私钥
gpg  -ao encrypt-subkey --export-secret-subkeys 6FE9C71CFED44076!    #导出有[E]标识、加密用子私钥 ,这里的ID替换为你的子密钥ID
# 别忘了同时将你刚刚生成的撤销凭证也备份起来
```

### 删除 delete

备份完后，要将本机的密钥清除干净，首先删除：

```shell
gpg --delete-secret-keys linus  # 删除私钥，  UID 也可以替换成子密钥ID, 主密钥Key ID
gpg --delete-keys linus   # 删除公钥

# 如果想全部删除推荐直接删文件夹,即删除 $HOME/.gnupg
```

### 导入 import key certification

```shell
# 从文件导入
gpg --import [密钥文件]   # 刚刚备份的子密钥文件， 或者其他人的公钥

# 暂不推荐从公钥服务器导入

# 输出
sec#   rsa3072/0x99F583599B7E31F1 2021-01-11 [SC]   # sec 后面带有 # 号说明主密钥未导入，是安全的
      Key fingerprint = 7053 58AB 8536 6CAB 05C0  220F 99F5 8359 9B7E 31F1 #指纹信息
uid                   [unknown] linus <linus@outlook.com>
ssb #    rsa3072/0x6FE9C71CFED44076 2021-01-11 [E]           # 带有 # 号说明该子密钥已导入
```

### 签名和验证 sign and verify

```shell
# 第一种方式，生成二进制签名文件
gpg --sign input.txt  # 当然也可以加上--output参数

# 第二种方式，生成ASCII格式签名
gpg --clearsign input.txt

# 第三种，签名和原文本分开（前两种的签名文件中包含了所有原文本，所以体积会比较大）
gpg --armor --detach-sign input.txt  #不加armor生成会二进制

#  验证签名文件
gpg --verify demo.txt.asc demo.txt
```

文件加解密：

```shell
# 加密：
# recipient指定接收者的公钥ID
gpg --recipient {keyid/uid} --output encrypt.txt --encrypt input.txt
# 也可以按喜好加上--armor选项等

# 我更喜欢用 
gpg  -se  -o  encrypt.txt  -r  {keyid/uid}   input.txt  
# s代表签名  e代表加密
# o是 将结果 输出到文件  encrypt.txt
# r后面跟 接收者的 uid或者 key id， 接收者的公钥必须已经导入过
# input.txt 是你要加密的文件


# 解密：
gpg --decrypt encrypt.txt --output decrypt.txt
# 也可以
gpg -d encrypt.txt   # 输出到终端 直接查看
```

### GPG Editer

```text
gpg --edit-key linus

quit：退出此菜单。
save：保存并退出。
help：显示帮助信息。
fpr：显示密钥指纹。
grip：显示密钥抓手。
list：列出密钥和用户ID。
uid：选择用户ID N。
key：选择子密钥N。
check：检查签名。
sign：签名选定的用户ID。
lsign：本地签名选定的用户ID。
tsign：用信任签名签名选定的用户ID。
nrsign：用不可撤销的签名签名选定的用户ID。
adduid：添加一个用户ID。
addphoto：添加一个照片ID。
deluid：删除选定的用户ID。
addkey：添加一个子密钥。
addcardkey：将密钥添加到智能卡。
keytocard：将密钥移动到智能卡。
bkuptocard：将备份密钥移动到智能卡。
delkey：删除选定的子密钥。
addrevoker：添加一个撤销密钥。
delsig：从选定的用户ID中删除签名。
expire：更改密钥或选定子密钥的过期日期。
primary：将选定的用户ID标记为主要的。
pref：列出偏好（专家）。
showpref：列出偏好（详细）。
setpref：为选定的用户ID设置偏好列表。
keyserver：为选定的用户ID设置首选密钥服务器URL。
notation：为选定的用户ID设置一个注解。
passwd：更改密码。
trust：更改所有者信任。
revsig：撤销选定用户ID上的签名。
revuid：撤销选定的用户ID。
revkey：撤销密钥或选定的子密钥。
enable：启用密钥。
disable：禁用密钥。
showphoto：显示选定的照片ID。
clean：压缩不可用的用户ID并从密钥中删除不可用的签名。
minimize：压缩不可用的用户ID并从密钥中删除所有签名。
```

## Reference 参考

1. [thehackernews: hackers-hijack-github-accounts-in](https://thehackernews.com/2024/03/hackers-hijack-github-accounts-in.html)
2. [GitHub Authentication](https://docs.github.com/en/authentication/)
