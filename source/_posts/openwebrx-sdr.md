---

title: 基于 Openwebrx 搭建 Rsp1 Websdr
date: 2023-04-29 15:51:48
updated: 2023-04-29 15:59:48
categories: Radio
tags: [Ham, Radio, 业余无线电, 软件无线电, Openwebrx]

---

# 基于 Openwebrx 搭建 Rsp1 Websdr

[Openwebrx](https://www.openwebrx.de/)

本文参考：[https://github.com/jketterl/openwebrx/wiki/](https://github.com/jketterl/openwebrx/wiki/)

## 安装依赖

```sh
sudo apt-get update
sudo apt-get install git build-essential cmake libfftw3-dev python3 python3-setuptools rtl-sdr netcat libsndfile-dev librtlsdr-dev automake autoconf libtool pkg-config libsamplerate-dev libpython3-dev
```

## Install Csdr

```sh
git clone -b master https://github.com/jketterl/csdr.git
cd csdr
mkdir build
cd build
cmake ..
make
sudo make install
cd ../..
sudo ldconfig
```

## Install Pysdr

```sh
git clone -b master https://github.com/jketterl/pycsdr.git
cd pycsdr
sudo python3 setup.py install install_headers
cd ..
```

## Install Python js8py Module

```sh
git clone -b master https://github.com/jketterl/js8py.git
cd js8py
sudo python3 setup.py install
cd ..
```

## Install Rsp1 Device

安装 rsp1 的 linux API 驱动,使用 API 3.07.

[Sdrplay 官网网站](https://www.sdrplay.com/software)

```sh
wget https://www.sdrplay.com/software/SDRplay_RSP_API-Linux-3.07.1.run
chmod 777 SDRplay_RSP_API-Linux-3.07.1.run
sudo ./SDRplay_RSP_API-Linux-3.07.1.run
```

## Install Soapysdr 驱动

[SoapySDR](https://github.com/pothosware/SoapySDR)

通过编译安装 SoapySdr 驱动, ~~或者通过 ​~~​~~`apt-get`~~​~~​ 软件包管理安装~~

```sh
git clone https://github.com/pothosware/SoapySDR
cd SoapySDR
mkdir build
cd build
cmake ..
make 
sudo make install
sudo ldconfig
cd ..
```

## Install Soapysdrplay 驱动

用于使得 Soapysdr 使得支持 sdrplay 设备.

```sh
git clone https://github.com/pothosware/SoapySDRPlay.git
cd SoapySDRPlay
mkdir build
cd build
cmake ..
make
sudo make install
```

## Install rx_tools

```sh
git clone https://github.com/rxseger/rx_tools
cd rx_tools
mkdir build
cd build
cmake ..
make 
sudo make install
sudo ldconfig
cd ..
```

## 加入内核 Blacklist

rtl_sdr 设备

```sh
nano /etc/modprobe.d/blacklist-rtlsdr.conf
blacklist dvb_usb_rtl28xxu
```

sdrplay 设备

```sh
nano /etc/modprobe.d/blacklist-sdrplay.conf
blacklist sdr_msi3101
blacklist msi001
blacklist msi2500
```

## 重启后测试是否可以识别 SDR 设备

```sh
SoapySDRUtil --find
```

## 安装 Openwebrx 主程序

```sh
# 新建数据文件
mkdir /var/lib/openwebrx
sudo sh -c "echo [] > /var/lib/openwebrx/users.json"

# download
git clone -b master https://github.com/jketterl/openwebrx.git
cd openwebrx
./openwebrx.py

# add admin user
./openwebrx.py admin adduser admin
```

## Install owrx_connector 链接组件

```sh
git clone -b master https://github.com/jketterl/owrx_connector.git
cd owrx_connector
mkdir build
cd build
cmake ..
make
sudo make install
cd ../..
sudo ldconfig
```

## 运行 Openwebrx

```sh
./openwebrx.py
```

访问 IP:8073 打开页面进行 setting.

## Openwebrx 自动控制瀑布增益

在 `openwebrx/htdocs/index.html` 末尾加入以下 JS 代码即可实现.

```html
<script type="text/javascript">
$(document).ready(function() {
$('#openwebrx-waterfall-colors-auto').contextmenu();
  setTimeout(function() { 
    $('#openwebrx-waterfall-colors-auto').contextmenu();
		$('#openwebrx-waterfall-colors-auto').click(); 
		}, 2000);
  $('#openwebrx-sdr-profiles-listbox').change(function() {
		console.log("change profiles!");
    $('#openwebrx-waterfall-colors-auto').contextmenu();
		setTimeout(function() { 
    $('#openwebrx-waterfall-colors-auto').contextmenu();
		$('#openwebrx-waterfall-colors-auto').click(); 
		}, 2000);
  });
});
</script>
```

## 参考引用 Reference

HamCQ.cn
