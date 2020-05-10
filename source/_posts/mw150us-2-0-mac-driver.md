---
title: 水星USB无线网卡mw150us苹果macOS系统驱动成功
date: 2020-05-10 15:04:06
tags: [MW150US,Driver,MacOS]
categories: MacOS
copyright: true
---

## 概述

​	之前修好后的**MacBook Pro (13-inch, Mid 2010)**，去年开始就发现偶尔找不到自带的无线网卡，用着也还经常死机。屏蔽了无线功能后，一直只能有线上网。最近终于忍不住，要无线上网了。。。由于囊中羞涩，先找了一块**MERCURY(水星)**的usb无线网卡MW150US 2.0 (170107)，想在**macOS** Hight Sierra 10.13.5上驱动它。

<!-- more -->

## 基本信息

​	由于水星这款网卡，不同时期生产的是不一样的芯片，我手头上这块是这样子的：

![水星无线网卡图片](https://alderaan.xyz/2020/05/10/mw150us-2-0-mac-driver/07576FCDC43828BAE9BB5A2FC846FA97.jpg)

​	在苹果系统上的硬件信息如下：

```
802.11n NIC：

  产品 ID：	0x8179
  厂商 ID：	0x0bda  (Realtek Semiconductor Corp.)
  版本：	0.00
  序列号：	00E04C0001
  速度：	最大 480 Mb/秒
  制造商：	Realtek
  位置 ID：	0x24100000 / 1
  可用电流 (mA)：	500
  所需电流 (mA)：	500
  额外的操作电流 (mA)：	0
```

​	在Windows系统上，驱动后显示的网卡名称包含`rtl 8188eu`字眼，成功得到对应芯片型号信息。

## 寻找驱动

​	一开始在网上下载一些别人试过的Mac版本驱动，安装后仍无法使用，有可能是我的系统版本比较新的缘故。转而开始从芯片版本入手，查找其他也使用了这款芯片的厂家，看是否有对应的macOS驱动。后来了解到，`Edimax`这个厂家的[EW-7811Un](https://www.edimax.com/edimax/merchandise/merchandise_detail/data/edimax/global/wireless_adapters_n150/ew-7811un/) 这款 USB 无线网卡，采用了类似芯片，也是最高150Mbps。在[Download](https://www.edimax.com/edimax/download/download/data/edimax/global/download/for_home/wireless_adapters/wireless_adapters_n150/ew-7811un)页面上有如下的驱动列表：

| Version  | Note                                                         | Date       | File Type | File Size |                                                              |
| -------- | ------------------------------------------------------------ | ---------- | --------- | --------- | ------------------------------------------------------------ |
| v1.0.1.4 | Support OS:MAC 10.7/10.8/10.9/10.10/10.11 Languages:English  | 2016-02-24 | ZIP       | 10.30 MB  | [![img](https://www.edimax.com/edimax/mw/cufiles/images/web/download_btn.png)](https://www.edimax.com/edimax/mw/cufiles/files/download/Driver_Utility/EW-7811Un_Mac_driver_v1.0.1.4.zip) |
| v1.0.0.1 | Support OS:Raspberry Pi2 Win10 IoT Languages:English         | 2015-12-21 | ZIP       | 1.65 MB   | [![img](https://www.edimax.com/edimax/mw/cufiles/images/web/download_btn.png)](https://www.edimax.com/edimax/mw/cufiles/files/download/Driver_Utility/EW-7811Un_Win10_IoT_1.0.0.1.zip) |
| v1.0.0.5 | Support OS:Windows XP/Vista Languages:English                | 2015-12-17 | ZIP       | 26.81 MB  | [![img](https://www.edimax.com/edimax/mw/cufiles/images/web/download_btn.png)](https://www.edimax.com/edimax/mw/cufiles/files/download/Driver_Utility/transfer/Wireless/NIC/EW-7811Un/EW-7811Un_Windows_driver_v1.0.0.5.zip) |
| v1.0.0.5 | Support OS:MAC 10.4/10.5/10.6 Languages:English              | 2012-12-12 | ZIP       | 24.73 MB  | [![img](https://www.edimax.com/edimax/mw/cufiles/images/web/download_btn.png)](https://www.edimax.com/edimax/mw/cufiles/files/download/Driver_Utility/transfer/Wireless/NIC/EW-7811Un/EW-7811Un_Mac_driver_v1.0.0.5.zip) |
| 1.0.1.6  | Support OS:Windows 7/8/8.1/10 Languages:English              | 2019-10-21 | ZIP       | 49.75 MB  | [![img](https://www.edimax.com/edimax/mw/cufiles/images/web/download_btn.png)](https://www.edimax.com/edimax/mw/cufiles/files/download/Driver_Utility/EW-7811Un_Windows_Driver_1.0.1.6.zip) |
| 1.0.1.8  | Support OS:MAC 10.13 Languages:English                       | 2018-09-17 | ZIP       | 12.03 MB  | [![img](https://www.edimax.com/edimax/mw/cufiles/images/web/download_btn.png)](https://www.edimax.com/edimax/mw/cufiles/files/download/Driver_Utility/EW-7811Un_Mac_driver_1.0.1.8.zip) |
| 1.0.1.9  | EW-7811Un_Linux_Wi-Fi_Driver_1.0.1.9 Support Kernel : 2.6.18 ~ 4.4.3 | 2018-06-13 | ZIP       | 1.08 MB   | [![img](https://www.edimax.com/edimax/mw/cufiles/images/web/download_btn.png)](https://www.edimax.com/edimax/mw/cufiles/files/download/Driver_Utility/EW-7811Un_Linux_Driver_1.0.1.9.zip) |
| 1.0.1.5  | Support OS:MAC 10.12 Languages:English                       | 2016-12-20 | ZIP       | 10.70 MB  | [![img](https://www.edimax.com/edimax/mw/cufiles/images/web/download_btn.png)](https://www.edimax.com/edimax/mw/cufiles/files/download/Driver_Utility/Mac10.12_Driver_1.0.1.5.zip) |

​	刚好有1.0.1.8这个版本可以支持macOS 10.13，直接下载解压后，有如下四个文件：

![压缩包解压后文件夹图片](https://alderaan.xyz/2020/05/10/mw150us-2-0-mac-driver/20200510-221203.png)

## 安装驱动

​	双击`Installer.pkg`安装驱动

![压缩包解压后文件夹图片](https://alderaan.xyz/2020/05/10/mw150us-2-0-mac-driver/20200510-221615.png)

​	选择继续

![压缩包解压后文件夹图片](https://alderaan.xyz/2020/05/10/mw150us-2-0-mac-driver/20200510-221624.png)

​	选择继续，会弹出如下窗口

![压缩包解压后文件夹图片](https://alderaan.xyz/2020/05/10/mw150us-2-0-mac-driver/20200510-221648.png)

​	同意软件许可协议中的条款

![压缩包解压后文件夹图片](https://alderaan.xyz/2020/05/10/mw150us-2-0-mac-driver/20200510-221713.png)

​	点击安装开始执行安装，这个过程可能需要几分钟，耐心等待

![压缩包解压后文件夹图片](https://alderaan.xyz/2020/05/10/mw150us-2-0-mac-driver/20200510-222045.png)

​	安装完成后会提示需要重新启动，同时在第一次运行会提示需要有额外的权限，允许即可。

## 完成

​	如果安装完成，在右上侧状态栏中会出现新的图标，插上USB网卡，成功驱动后会显示周围的WiFi网络：

![驱动成功周围WiFi信息截图](https://alderaan.xyz/2020/05/10/mw150us-2-0-mac-driver/20200510-224242.png)

​	打开Wireless Utility显示信息如下，左上角的图标和网上其他人发的那些驱动图标一模一样：

![Wireless Utility显示信息截图](https://alderaan.xyz/2020/05/10/mw150us-2-0-mac-driver/20200510-224641.png)

​	现在就可以开心地重新享受无线上网了，其他81XX芯片型号的网卡也可以试试这个厂家的驱动，对于黑苹果用户也可以参考使用。

