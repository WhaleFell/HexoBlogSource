---
title: ð æ èæ´¾æè¾æå (ä¸)ââåå¤ç³»ç» ð
date: 2021-06-18 19:37:48
updated: 2021-06-18 19:37:48
categories: Linux
tags: [Respi, Linux]
description: 
index_img: http://pic.lskyl.xyz/blog/old/20210619154345.jpg
banner_img: http://pic.lskyl.xyz/blog/old/20210619154345.jpg
---

# ð æ èæ´¾æè¾æå (ä¸)ââåå¤ç³»ç» ð

## ç§å½å®æ¹`Debian 10 buster`ç³»ç»éå

 åç¨ **SDFormatter** æ ¼å¼åä¸ä¸åå­å¡å­:

![hZkDB8qxtRgQz3S](http://pic.lskyl.xyz/blog/old/20210619152409.png-picsmall)

### 1. ç§åéåç¨å°çè½¯ä»¶ï¼ **Win32 Disk Image**

![Zb6CEHnf17oqO5Q](http://pic.lskyl.xyz/blog/old/20210619152403.png-picsmall)

- ç¶åå¨Uççæ ¹ç®å½å»ºç«ä¸ä¸ªç©ºç½ç **sshæä»¶  æ¹ä¾¿sshè¿ç¨è¿æ¥**
   ![FV5qpvWz7LtsOgi](http://pic.lskyl.xyz/blog/old/sasw.png-picsmall)
- ç¨ **Windows PowerShell** è¿æ¥æ èæ´¾ssh
  `shift+å³é®` å¼åº**Windows PowerShell**
  **å®æ´è¿æ¥è¯­æ³**:

```shell
ssh -p ç«¯å£å· ç¨æ·å@ä¸»æºå°å
```

> æ èæ´¾é»è®¤çç¨æ·å **pi** å¯ç  **raspberry**![QLA74lscbwzRWY2](http://pic.lskyl.xyz/blog/old/20210619152448.png-picsmall)

- æ èæ´¾æ©å±TFå¡ååº:
   sudo raspi-config --> Advanced options -->Expand Filesystem, ç¡®è®¤éå¯

### 2. å¯å¨æ èæ´¾HDMIåè½

- ç¼è¾`config.txt`æä»¶ï¼ä¿®æ¹ä»¥ä¸åæ°:
  sudo nano /boot/config.txt

  - æä¸é¢#æ³¨éç¬¦å·å»æ
    hdmi_force_hotplug=1  #å¯ç¨HDMIç­ææåè½
    config_hdmi_boost=4    #å¯ç¨å å¼ºHDMIä¿¡å·

  > ä¸åºæå¤çè¯åºè¯¥å¯ä»¥æ¥ä¸ï¼ä½æ¯æçæ²¡æå£°é³è¾åºè¯¶
  > æ³¨ï¼å¦æè¿æ¯ä¸è½çè¯ï¼æ¾å°#hdmi_group=1è¿å¥è¯ï¼æåé¢ç#æ³¨éç¬¦å·å»æï¼ææ°å­æ¹æ 2å¼ºè¡æå®æ¾ç¤ºå¨ç±»åï¼1æ¯è¿æ¥èå¼çµè§ï¼2ä»£è¡¨è¿æ¥æ°çµè§ã

## æ èæ´¾ `Debian 10 buster` æ¢æ¸åæº

```shell
sudo nano /etc/apt/sources.list
deb http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main non-free contrib
deb-src http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main non-free contrib

sudo nano /etc/apt/sources.list.d/raspi.list
deb http://mirrors.tuna.tsinghua.edu.cn/raspberrypi/ buster main ui
```

- æ´æ°æºåè¡¨: `sudo apt-get update`
- æ´æ°è½¯ä»¶çæ¬ï¼åçº§è½¯ä»¶å: `sudo apt-get upgrade`

## æ èæ´¾`rasp-config`ç¸å³è®¾ç½®

### 1. è®¾ç½®piï¼rootç¨æ·å¯ç ï¼å¹¶è§£é

```shell
#æ èæ´¾ä¿®æ¹å¯ç ï¼é¡ºä¾¿è§£érootç¨æ·
sudo passwd pi
sudo passwd root
#è§£érootç¨æ·
sudo passwd --unlock root
#å¦ææ³å¨ç»ç«¯ç´æ¥ç¨rootç¨æ·ç»å½ï¼ç¼è¾
sudo nano /etc/ssh/sshd_config
ä¿®æ¹ PermitRootLogin without-password ä¸º PermitRootLogin yes
```

### 2. **respiæ¬å°å**æä½

- å®è£ä¸­æå­ä½ï¼æä¾å ä¸ªLinuxä¸­æå­ä½åº:

 ```shell
sudo apt-get install xfonts-wqy
sudo apt-get install ttf-wqy-zenhei ttf-wqy-microhei
 ```

- è®¾ç½®ç»ç«¯ä¸­ææ¾ç¤º: `sudo raspi-config`:
  éæ©change_localeï¼å¨Default locale for the system environment:ä¸­éæ©zh_CN.UTF-8ã
  å¾ä¸ç¿»ä¸ä¼å¿ç´å°æ¾å°zh_CN UTF-8æåæ ç§»å¨å°åé¢ï¼ç¶åæä¸ç©ºæ ¼é®æä¸*
  ![3hQeD8k9L1mgTZc](http://pic.lskyl.xyz/blog/old/20210619152513.png-picsmall)

- æ¹åé®çå¸å±: `sudo dpkg-reconfigure keyboard-configuration`

### 3. è®¾ç½® vncæ¡é¢ è¿æ¥

> æ³¨:è¿éæ¾å¼äºæ èæ´¾èªå¸¦ç **realvnc** å ä¸ºä¸æ¯æç½é¡µ **novnc** ä¸åè½å¾å°ï¼æä»¥ç¨ **Tightvnc** ä»£æ¿

- å®è£**Tightvncserver**: `sudo apt-get install tightvncserver`
- å®è£å¥½ä¹åè®¾ç½®ä¸ä¸ª**VNCå¯ç **:  vncpasswd

> æ³¨: åè¾å¥æä½å¯ç ä¸¤æ¬¡ï¼ç¶åä¼è¯¢é®æ¯å¦è®¾ç½®ä¸ä¸ªæ¥ç(view-only)å¯ç ï¼æèªå·±åæ¬¢ï¼ä¸è¬æ²¡å¿è¦ã

- è®¾ç½®**å¼æºèªå¯å¨** :

>è®¾ç½®**å¼æºå¯å¨**ï¼éè¦å¨ **/etc/init.d/** ä¸­åå»ºä¸ä¸ªæä»¶ãä¾å¦**tightvncserver**:  (å¯å¨èæ¬çåç§°ï¼æåç¨åºåä¸è´çä¹ æ¯)

```sh
sudo nano /etc/init.d/tightvncserver
# åå®¹å¦ä¸:
#!/bin/sh
### BEGIN INIT INFO
# Provides:          tightvncserver
# Required-Start:    $local_fs
# Required-Stop:     $local_fs
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: Start/stop tightvncserver
### END INIT INFO

# More details see:
# http://www.penguintutor.com/linux/tightvnc

### Customize this entry
# Set the USER variable to the name of the user to start tightvncserver under
export USER='pi'
### End customization required

eval cd ~$USER

case "$1" in
  start)
    # å¯å¨å½ä»¤è¡ãæ­¤å¤èªå®ä¹åè¾¨çãæ§å¶å°å·ç æå¶å®åæ°ã
    su $USER -c '/usr/bin/tightvncserver -depth 16 -geometry 800x600 :1'
    echo "Starting TightVNC server for $USER "
    ;;
  stop)
    # ç»æ­¢å½ä»¤è¡ãæ­¤å¤æ§å¶å°å·ç ä¸å¯å¨ä¸è´ã
    su $USER -c '/usr/bin/tightvncserver -kill :1'
    echo "Tightvncserver stopped"
    ;;
  *)
    echo "Usage: /etc/init.d/tightvncserver {start|stop}"
    exit 1
    ;;
esac
exit 0
```

ç¶åç»**tightvncserveræä»¶**å **æ§è¡æé**ï¼

```shell
sudo chmod 755 /etc/init.d/tightvncserver
```

å¹¶æ´æ°**å¼æºå¯å¨åè¡¨**ï¼

```shell
sudo update-rc.d tightvncserver defaults
```

ä¸äº**serviceå½ä»¤** :

```shell
sudo service tightvncserver restart #éå¯æå¡
sudo service tightvncserver start/stop #å³é­/å¼å¯æå¡
sudo service tightvncserver status #æ¥çæå¡è¿è¡ç¶æ
```

> é:   vncå®¢æ·ç«¯ä¸è½½
> [vncå®ç½](https://www.realvnc.com/en/connect/download/viewer/)

è¿æ¥æåæ¹~:
![](http://pic.lskyl.xyz/blog/old/20210619152521.png-picsmall)
![](http://pic.lskyl.xyz/blog/old/20210619152531.png-picsmall)
ç¼è¾ ./vnc/xstartup éç½®æä»¶ä½¿å¶è½ä¸windownå±äº«åªè´´æ¿

```shell
sudo nano .vnc/xstartup

#å¨åæ¹è¿½å 
vncconfig -nowin -iconic &
#éå¯Tightvncserver
sudo service tightvncserver restart
```

### 4.é¨ç½²**novncç½é¡µ**

> æ¹ä¾¿å¨ç½é¡µä¸æ§å¶æ èæ´¾å±å¹ï¼ä½æ¯**ä¸æ¯ærealvnc**

- å®è£ **git æ¯æ**

```shell
sudo apt-get install git
```

- **åé** novncé¡¹ç®:

> å¨ä¸­å½å¤§éå¬è¯´~~å ä¸ *<https://gproxy.cn>* å°±å¯ä»¥å é **åé** éåº¦ä¸«~~ æ¹å£ åºæ¢æ  [https://github.com.cnpmjs.org/](https://github.com.cnpmjs.org/)

```shell
git clone https://github.com/kanaka/noVNC #æºå°å
git clone https://github.com.cnpmjs.org/kanaka/noVNC #å éå°å
```

- è¿è¡ **novnc** å¹¶è®¾ç½®**å¼æºèªå¯å¨**:

```shell
cd noVNC
# åå§åå¯è½æç¹ç¹æ¢
./utils/launch.sh --vnc localhost:5901 #çå¬5901 vncç«¯å£
```

![](http://pic.lskyl.xyz/blog/old/20210619152538.png-picsmall)

- å°è¯è®¿é®: [http://raspiberry:6080](http://raspiberry:6080)  å¯
  ![](http://pic.lskyl.xyz/blog/old/20210619152542.png-picsmall)
- ä¸äº**é«çº§è®¾ç½®** :

```shell
./utils/websockify --web ./ 8787 localhost:5901  #ä¿®æ¹6080é»è®¤ç«¯å£
./utils/websockify --web ./ 8787 192.168.1.10:5901 #å¯ä»¥è®²localhostæ¹æææå®è£äºvncserverçIPå°å
```

 **è¿æ¥éåº¦å¤ªæ¢å¯ä»¥å®è£Pythonçnumpyåºè§£å³**

- è®¾ç½®**å¼æºå¯å¨**ï¼

```shell
#ç¼è¾å¼æºå¯å¨é¡¹
sudo nano /etc/rc.local
#ä»¥piç¨æ·è¿è¡ç¨åº
su pi -c "/home/pi/noVNC/utils/launch.sh --vnc localhost:5901" &
```

![fEVci5dlNCMaUs4](http://pic.lskyl.xyz/blog/old/20210619152617.png-picsmall)

### 5.å®è£`cockpit` webå¯è§åç®¡ç

```shell
sudo apt-get update
sudo apt-get install cockpit
```

> å®è£çä¾èµæ  **ä¸ä¸¢ä¸¢å¤**

- é»è®¤æ¯ç¨`https`è®¿é®ï¼éè¦ä¿®æ¹éç½®æä»¶ä½¿å¶è½`http`è®¿é®

```shell
sudo nano /etc/cockpit/cockpit.conf #è¿ä¸ªæä»¶é»è®¤æ¯ä¸å­å¨çéè¦æ°å»º

[WebService]
AllowUnencrypted=true
LoginTitle=é²¸äºpi
```

- éç½®**å¼æºå¯å¨**

```shell
sudo systemctl enable cockpit.socket
sudo systemctl start cockpit.socket
```

- é¡µé¢

  ![image-20200805174634421](http://pic.lskyl.xyz/blog/old/20210619152626.png-picsmall)

## Pythonè®¾ç½®

### æ¦åµ

> æ èæ´¾é»è®¤å®è£äºä¸¤ä¸ªçæ¬ç**Python**

![](http://pic.lskyl.xyz/blog/old/20210619152632.png-picsmall)

### æ èæ´¾pipæ¢æº

>**pip**æ´æ¢ä¸º**å½åæº**ï¼å¯ä»¥å¤§å¤§çæé«å®è£æåçåéåº¦ãä¸ç®¡ä½ ç¨çæ¯**pip3è¿æ¯pipï¼æ¹æ³é½æ¯ä¸æ ·ç**

```shell
mkdir ~/.pip
nano ~/.pip/pip.conf

#åå¥
[global]
timeout = 5000
index-url = https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple
[install]
use-mirrors = true
mirrors = https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple
```

- **æ´æ°pipçæ¬**

```shell
python3 -m pip install --upgrade pip
```

![seVOiSZrBKgE827](http://pic.lskyl.xyz/blog/old/20210619152649.png-picsmall)

- **æ èæ´¾æå®Pythonçæ¬å®è£æ¨¡å**

```shell
sudo pip3 install XXX   #Python3çæ¬
sudo pip install XXX   #Python2çæ¬
```

## é¨ç½²zsh

```shell
sh -c "$(wget -O- https://gitee.com/mirrors/oh-my-zsh/raw/master/tools/install.sh)"

git clone https://github.com.cnpmjs.org/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions

 git clone https://github.com.cnpmjs.org/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
 
 ZSH_DISABLE_COMPFIX=true
```
