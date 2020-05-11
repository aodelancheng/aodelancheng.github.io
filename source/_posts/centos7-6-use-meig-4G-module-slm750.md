---
title: Centos 7.6 下使用美格SLM750（4G模块）拨号上网
date: 2020-05-11 17:40:13
tags: [Centos,4G module,SLM750]
categories: Centos
---

## 概述

​	想要实现4G上网有两种方式，要么加多一个4G路由器，再通过优先接入；要么通过增加4G模块（可为USB或PCIE等多种接口），直接进行拨号上网。尝试在一款J1900工控机上（该工控机自带SIM插槽），通过增加PCIE接口的美格4G模块`SLM750`，进行拨号上网。Windows系统下已测试过，直接安装厂家提供驱动，可以正常上网，说明硬件方面是完全支持的。本文参照厂家提供的嵌入式方案，进行驱动编译安装，并编译拨号软件，最终实现工控机4G上网功能。

<!-- more -->

## 准备

​	系统为Cento 7.6 64bit，基本环境为Basic Web Server安装（理论上与安装环境模式无关，最小安装也可以）。需要下载内核源码，Centos 7.6的内核版本为`3.10.0-957`，源码可在此[链接](http://vault.centos.org/7.6.1810/updates/Source/SPackages/kernel-3.10.0-957.21.3.el7.src.rpm)下载。另外还需要厂家提供的`GobiNet`网卡拨号的驱动及拨号工具源码，一张能4G上网的手机卡或物联网卡，接好模块天线。

## 编译内核源码

​	将下载好的源码，解压到看到`linux-3.10.0-957.21.3.el7.tar.xz`文件，将其放到`/usr/src/kernels`文件夹下，并执行如下命令：

```bash
$ tar xvf linux-3.10.0-957.21.3.el7.tar.xz // 解压内核源码文件
$ mv linux-3.10.0-957.21.3.el7 3.10.0-957.el7.x86_64 // 重命名文件夹
```

​	之所以要更改文件夹名称，是因为厂家的`GobiNet`驱动源码，`Makefile`文件中：

```makefile
obj-m := GobiNet.o
GobiNet-objs := GobiUSBNet.o QMIDevice.o QMI.o

PWD := $(shell pwd)
OUTPUTDIR=/lib/modules/`uname -r`/kernel/drivers/net/usb/

#ifeq ($(ARCH),)
#EARCH := $(shell uname -m)
#endif
#ifeq ($(CROSS_COMPILE),)
#CROSS_COMPILE :=
#endif
#ifeq ($(KDIR),)
KDIR := /lib/modules/$(shell uname -r)/build # 这里通过uname -r 获取了内核名称
#endif

default:
#	ln -sf makefile Makefile
	#$(MAKE) ARCH=${ARCH} CROSS_COMPILE=${CROSS_COMPILE} -C $(KDIR) M=$(PWD) modules
	$(MAKE)  CROSS_COMPILE=${CROSS_COMPILE} -C $(KDIR) M=$(PWD) modules

install: default
	mkdir -p $(OUTPUTDIR)
	cp -f GobiNet.ko $(OUTPUTDIR)
	depmod
	modprobe -r GobiNet
	modprobe GobiNet

clean:
	# rm -rf Makefile # 这里这段代码去掉，否则执行make clean会把Makefile文件也删除了
	rm -rf *.o *~ core .depend .*.cmd *.ko *.mod.c .tmp_versions Module.* modules.order
```

​	如果为其他版本的系统，将文件夹对应修改为`uname -r`得到的名称即可。

### 添加串口的ID

​	打开内核源码文件 `/3.10.0-957.el7.x86_64/drivers/usb/serial/option.c`，在`/* Vendor and product IDs */`下增加宏定义：

```c
/* Vendor and product IDs */

#define MEIG_VENDOR_ID				0x05C6 
#define MEIG_PRODUCT_730			0xF601 
#define MEIG_VENDOR_ID_720			0x2dee 
#define MEIG_PRODUCT_720			0x4d07 
#define MEIG_PRODUCT_720_ECM			0x4d02 
```

​	在`option_ids`结构体数组增加4G模块的`VID`和`PID`：

```c
static const struct usb_device_id option_ids[] = {
	{ USB_DEVICE(MEIG_VENDOR_ID,MEIG_PRODUCT_730) }, 
	{ USB_DEVICE(MEIG_VENDOR_ID_720,MEIG_PRODUCT_720) }, 
	{ USB_DEVICE(MEIG_VENDOR_ID_720,MEIG_PRODUCT_720_ECM) }, 
```

### 删除NDIS和ADB端口

​	使用`option`驱动，修改 `/3.10.0-957.el7.x86_64/driver/usb/serial/option.c`，在`option_probe`函数添加如下代码：

 ```c
static int option_probe(struct usb_serial *serial,
			const struct usb_device_id *id)
{
	struct usb_interface_descriptor *iface_desc =
				&serial->interface->cur_altsetting->desc;
	struct usb_device_descriptor *dev_desc = &serial->dev->descriptor;
	const struct option_blacklist_info *blacklist;

	/* Never bind to the CD-Rom emulation interface	*/
	if (iface_desc->bInterfaceClass == 0x08)
		return -ENODEV;

	/*
	 * Don't bind reserved interfaces (like network ones) which often have
	 * the same class/subclass/protocol as the serial interfaces.  Look at
	 * the Windows driver .INF files for reserved interface numbers.
	 */
	blacklist = (void *)id->driver_info;
	if (blacklist && test_bit(iface_desc->bInterfaceNumber,
						&blacklist->reserved))
		return -ENODEV;

	// struct usb_wwan_intf_private *data; 文档中的这个语句其实没有
	// 开始添加代码
	if (serial->dev->descriptor.idVendor == MEIG_VENDOR_ID && 
		(serial->dev->descriptor.idProduct == MEIG_PRODUCT_730)&& 
		serial->interface->cur_altsetting->desc.bInterfaceNumber >= 4) 
			return -ENODEV; 

	if (serial->dev->descriptor.idVendor == MEIG_VENDOR_ID_720&& 
		(serial->dev->descriptor.idProduct == MEIG_PRODUCT_720)&& 
		serial->interface->cur_altsetting->desc.bInterfaceNumber >= 4) 
			return -ENODEV; 
	// 完成添加代码
 ```

使用`usb-serial`驱动，`/3.10.0-957.el7.x86_64/driver/usb/serial/usb-serial.c`，在`usb_serial_probe`函数添加如下代码：

```c
     serial = create_serial (dev, interface, type); 
    if (!serial) { 
            unlock_kernel(); 
            dev_err(&interface->dev, "%s - out of memory\n", __FUNCTION__); 
            return -ENOMEM; 
    } 
	//开始添加代码 厂家文档写的是宏定义，在该文件中无法找到会报错，这里直接改成了对应值
    if ( (serial->dev->descriptor.idVendor == 0x50C6 &&  
 		(serial->dev->descriptor.idProduct == 0xF601) )&& 
		serial->interface->cur_altsetting->desc.bInterfaceNumber >=4 ) 
			return -ENOMEM; 

	if (serial->dev->descriptor.idVendor == 0x2dee && 
		(serial->dev->descriptor.idProduct == 0x4d07)&& 
		serial->interface->cur_altsetting->desc.bInterfaceNumber >= 4) 
			return -ENODEV; 
	//完成添加代码
```
	### 配置编译参数

```bash
$ cd /usr/src/kernels/3.10.0-957.el7.x86_64  # 切换到内核源码所在路径
$ cp /boot/config-3.10.0-957.el7.x86_64 ./.config # 拷贝当前内核的编译配置
$ make oldconfig # 在已有内核基础上进行配置
$ yum install gcc gdb make elfutils-libelf-devel
```

​	需要说明的是，Centos 6.7默认就开启了`device drivers->usb support->usb serial converter support->USB driver for GSM and CDMA modems `和`device drivers->Network device support->usb Network Adapters->Multi-purpose USB Networking Framework `这两个组件，所以拷贝原有内核的编译配置即可使用。

### 开始编译

​	执行如下命令开始编译源码，对应的线程数字按照实际机器进行配置，这个过程会比较慢。

```bash
$ make -j 8
```

​	如果有其他错误提示，则安装对应的软件包依赖即可，这里编译后不进行安装，因为内核是一样的，编译内核只是为了编译驱动时能找到一些依赖。

## 编译NDIS驱动

​	这里采用的是单独编译的方式，所以上一个步骤没有和内核一块打包编译，主要是为了在不动原来内核的情况下使用，以防上面的其他软件运行受影响。

​	到驱动目录下，执行如下命令：

```bash
$ make # 编译驱动
$ make install # 安装驱动
```

​	正常编译安装的话，不会有其他的警告或者错误，驱动成功后，可以看到新的网卡。一般是`ethX`这种格式，但还没有IP地址，需要使用拨号软件。

## 编译Gobinet拨号工具

​	在厂家提供的源码中，由于是嵌入式的方案，默认获取IP地址的是`busybox`中的`udhcpc`命令，在`udhcpc.c`文件中，可以注释掉这样代码，以及这行代码上面两行的寻找默认配置文件的语句。本文管理网卡的工具是`NetworkManager`，Gobinet拨号后，会自动检测网卡状态，进行获取IP地址操作。其它系统根据实际需要，进行修改。本文做出的修改如下：

```c
if (profile->ipv4.Address) {
#ifdef USE_DHCLIENT
            snprintf(udhcpc_cmd, sizeof(udhcpc_cmd), "dhclient -4 -d --no-pid %s", ifname);
            dhclient_alive++;
#else
    		// 注释掉获取默认配置文件
            //if (access("/usr/share/udhcpc/default.script", X_OK)) {
            //    dbg_time("Fail to access /usr/share/udhcpc/default.script, errno: %d (%s)", errno, strerror(errno));
            //}

            //-f,--foreground    Run in foreground
            //-b,--background    Background if lease is not obtained
            //-n,--now        Exit if lease is not obtained
            //-q,--quit        Exit after obtaining lease
            //-t,--retries N        Send up to N discover packets (default 3)
    		// 注释定义的获取IP命令
            //snprintf(udhcpc_cmd, sizeof(udhcpc_cmd), "busybox udhcpc -f -n -q -t 5 -i %s", ifname);
#endif
			// 注释掉命令线程
            //pthread_create(&udhcpc_thread_id, &udhcpc_thread_attr, udhcpc_thread_function, (void*)strdup(udhcpc_cmd));
            sleep(1);
        }
```

​	执行如下命令编译拨号工具

```bash
$ make # 编译
$ ./MeiG-CM & # 后台执行拨号工具
```

​	如果拨号成功，可以看到对应的网卡会获取到IP地址，并可以正常上网。

## 服务化拨号工具

​	可以使用`systemctl`管理拨号工具，新建一个文件`MeiG-CM.service`，并写入如下内容：

```bash
[Unit]
Description=quectel-CM Service
After=network.target

[Service]
Type=simple
User=root
Restart=always
RestartSec=5s
ExecStart=/home/MeiG-CM/MeiG-CM # 这里更改为对应的可执行文件所在路径

[Install]
WantedBy=multi-user.target
```

​	执行以下命令可配置服务并设置开机自启动：

````bash
$ cp MeiG-CM.service /usr/lib/systemd/system/ # 拷贝服务文件到系统目录
$ systemctl daemon-reload # 重新检测加载服务，使其被系统识别到
$ systemctl start MeiG-CM.service # 手动启动服务
$ systemctl enable MeiG-CM.service # 配置开机自启动
````

​	至此，在Centos 7.6上就可以自动配置MeiG SLM50 4G模块上网。如果卡被停用后·再启用，也不需要重新启动机器，会自动重新拨号。