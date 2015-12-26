title: Gentoo安装小记
date: 2015-12-26 10:33:44
categories:
- Linux
tags:
- linux
- gentoo
---

最近由于某些原因重新安装了我的[Gentoo](http://gentoo.org)系统，虽然之前可以顺利的安装并使用，但再次安装的时候还是会遇到许多问题，趁此机会，将安装中遇到问题及解决方法记录一下。

# 安装Gentoo

Gentoo最常见的安装方式是使由stage3开始安装。stage3是一系列已编译好的基础工具链，也可以看作是一个简单的基本系统。如果喜欢折腾，也可以选择由stage1或是stage2开始安装。但很多时候并没有这样的必要。

## 下载stage3

我们可以由Gentoo官网或是某个速度较快的镜像站点下载，例如，我们可以选择[阿里云镜像](http://mirrors.aliyun.com)。

## 准备硬盘分区

准备要安装系统的硬盘分区，并进行格式化，例如我要安装在`/dev/sda2`这个分区：

    mkfs.ext4 /dev/sda2

## 解压stage3

挂载已准备好的安装分区：

    mount /dev/sda2 /mnt

将下载的stage3压缩包解压到准备好的分区中：

    cd /mnt
    tar -xvf ~/stage3.tar.bz2

## 准备chroot环境

挂载必要的系统文件，准备chroot环境：

    mount -t proc /proc /mnt/proc
    mount --rbind /sys /mnt/sys
    mount --make-slave /mnt/sys
    mount --rbind /dev /mnt/dev
    mount --make-slave /mnt/dev

为了能够顺利地`emerge`整个系统，我们需要能够正确地解析DNS：

    cp /etc/resolv.conf /mnt/etc

## chroot进入Gentoo系统

在准备好这些环境之后，我们就可以chroot进入这个最小的Gentoo系统了：

    chroot /mnt

更新环境变量，为`emerge`做好准备：

    source /etc/profile
    env-update

这样以后，我们就拥有了一个完全可用的Gentoo系统。接下的工作就是要使用强大的`emerge`来Emerge整个世界了。

## 准备portage

portage是一个描述如何下载、编译以及安装软件的元数据集。`emerge`正是通过portage来管理整个Gentoo系统的。

`/etc/portage/repos.conf`目录下存放在portage仓库的配置文件，例如主portage仓库的配置文件为`/etc/portage/repos.conf/genntoo.conf`，其内容如下：

    [DEFAULT]
    main-repo = gentoo

    [gentoo]
    location = /usr/portage
    sync-type = webrsync
    sync-uri  = rsync://rsync.aliyun.com/gentoo-portage

在这里需要注意的就是`sync-type`与`sync-uri`指定了portage的更新方式与更新地址，而摒弃了传统的在`/etc/portage/make.conf`指定portage更新地址的做法。

## 同步portage

将portage树同步到本地目录：

    emerge-webrsync

## 配置make.conf

我们可以一些全局的编译安装配置选择写入`/etc/portage/make.conf`文件中：

```bash
LINGUAS="zh_CN"
INPUT_DEVICES="evdev keyboard mouse synaptics"
VIDEO_CARDS="intel nvidia"

GENTOO_MIRRORS="http://mirrors.aliyun.com/gentoo/" 
```

在这里需要注意以下几项：

* INPUT_DEVICES指定了本机的输入设备
* VIDEO_CARDS指定了本机的显卡类型
* GENTOO_MIRRORS指定了我们要的仓库镜像

## 安装Linux内核

这也许是我们要安装的第一个portage包吧。

    emerge -av gentoo-sources

安装成功后，我们会在`/usr/src`目录下看到解压后的Linux内核目录以及一个指定该目录的`linux`链接。

## 配置安装Linux内核

进入Linux内核目录：

    cd /usr/src/linux
    make menuconfig

这样以后，我们就可以看到内核配置菜单。Linux的功能模块可以编译进入内核（以`*`标识）也可以编译为可动态加载的模块（以`M`标识），可以通过空格键进行选择切换。在配置Linux编译选项时，我们需要注意以下几点：

* 将Gentoo所在分区的文件系统格式编译进入内核，例如，我选择使用`ext4`作为Gentoo的文件系统，则需要将`ext4`编译进入内核，而将其他的文件系统格式编译为模块。

* 根据自己的硬件设备选择功能模块，例如，我的Acer上装有一个块Atheros AR9462无线网卡，则需要在设备驱动下Aheros支持中选中`ath9k`功能模块。

```
CONFIG_ATH9K_HW=m
CONFIG_ATH9K_COMMON=m
CONFIG_ATH9K_BTCOEX_SUPPORT=y
CONFIG_ATH9K=m
CONFIG_ATH9K_PCI=y
CONFIG_ATH9K_AHB=y
CONFIG_ATH9K_DEBUGFS=y
# CONFIG_ATH9K_STATION_STATISTICS is not set
CONFIG_ATH9K_DYNACK=y
# CONFIG_ATH9K_WOW is not set
CONFIG_ATH9K_RFKILL=y
# CONFIG_ATH9K_CHANNEL_CONTEXT is not set
CONFIG_ATH9K_PCOEM=y
# CONFIG_ATH9K_HTC is not set
```

* 在无线配置部分，选中`WEXT`模块：

```
CONFIG_WEXT_CORE=y
CONFIG_WEXT_PROC=y
CONFIG_CFG80211_WEXT=y
```

在完成所需要的配置后就可以进行内核编译了：

    make -j4

编译完成之后，安装内核模块：

    make modules_install

将编译得到的内核拷贝到`/boot`目录下：

    cp arch/x86_64/boot/bzImage /boot/kernel
    cp .config /boot/config
    cp System.map /boot

这样我们就得到可以用来启动系统的内核了。

## 配置/etc/fstab

`/etc/fstab`文件中配置了系统启动时所需要的分区信息，我们可以根据我们的实际情况进行配置，例如：

```
# <fs>			<mountpoint>	<type>		<opts>		<dump/pass>

# NOTE: If your BOOT partition is ReiserFS, add the notail option to opts.
UUID=388db5b1-c74e-4db5-8624-4218dcbaad79		/		ext4		noatime		0 1
UUID=ec2e513d-3fb0-4dba-8ab0-d363ce6c20e1		none		swap		sw		0 0
```

UUID值可以通过下列命令来查看：

    ls -l /dev/disk/by-uuid

## 安装必要的系统工具

日志工具：

    emerge --ask app-admin/sysklogd
    rc-update add sysklogd default

`cron`工具：

    emerge --ask sys-process/cronie
    rc-update add cronie default

文件索引工具：

    emerge --ask sys-apps/mlocate
    updatedb

这样以后当我们需要查找某个文件时就可以通过`locate`命令来定位了。

## 安装配置启动器

我们可以使用`grub`来管理系统启动：

    emerge -av sys-boot/grub

将`grub`安装到磁盘引导扇区：

    grub-install /dev/sda

这样以后我们就可以使用编译得到的内核来引导Gentoo系统了。

# 配置无线网络

在现代信息社会中，没有无线网格几乎是无法想像的一件事情。因而在新系统启动之后配置无线网络似乎就成为我们的当务之急。当然，相关的配置操作也可以在宿主系统中完成。

如果一切正常，在Gentoo系统启动后，我们可以通过`ifconfig`命令来查看当前系统的网络接口，包括有线与无线接口。下面我们着重记录一下相关的无线配置。

安装必需的工具：

    emerge -av net-wireless/wpa_supplicant

wpa_supplicant的配置位于`/etc/wpa_supplicant/wpa_supplicant.conf`中：

```
ctrl_interface=/var/run/wpa_supplicant
ctrl_interface_group=wheel
ap_scan=1
```

使无线接口在系统启动时自动启动：

    ln -s /etc/init.d/net.lo /etc/init.d/net.wlp3s0

其中，`wlp3s0`是我机器上的无线接口名称。

推荐使用`wicd`来管理网络连接：

    emerge -av wicd

有了自由的无线网络，接下来我们就可以开始快乐的Gentoo之旅了。

# 安装X

作为个人桌面使用的Gentoo，我们并不想每天都是面对黑黑的终端窗口工作，我们也希望看到五彩斑澜的精彩世界，这一切都需要神秘的X：

    emerge xorg-server

# 安装XFCE

X仅是一个显示服务器，而我们日常接触更多的则是各种桌面管理器或桌面环境。在这里推荐简约小巧的[XFCE](http://xfce.org)：

    emerge -av xfce4-meta

当然，如果你觉得这样安装的软件包过多，我们也可以选择我们需要的软件包来安装：

    emerge -av xfce4-session
    emerge -av xfce4-terminal
    emerge -av xfce4-panel
    emerge -av xfwm4

或者在`/usr/portage/xfce-base`下选择自己所需要的软件包来安装。

XFCE就是通往世界的一扇窗，我们可以由这里开始精彩的Gentoo之旅游。
