title: Debian中的Radeon驱动配置
date: 2013-06-25 22:23
categories: 
- Linux
tags: 
- debian
- radeon
---

在Debian上一直使用sid源。前一段时间sid源中的radeon驱动升级到了6.14.4-6。由于之前曾经升级到6.14.4-5，一度导致不能启动Debian的图形界面，所以对6.14.4-6版本的radeon驱动抱以极大的期望，希望能够正常的启动X界面，然而升级后依然是黑乎乎的字符界面。

查看/var/log/Xorg.0.log，可以看到是由于不能找到/usr/lib/i386-linux-gnu/dri/r600_dri.so，Google一下发现需要安装libgl1-mesa-dri：

	apt-get install libgl1-mesa-dri

安装该软件包后依然不能正常启动图形界面，查看日志文件，可以看到类似如下的错误：

>dri does not export required DRI extension

使用Google在网络世界中搜索一下后发现，原来是没有安装相应的firmware。可以使用下面的命令进行安装：

	apt-get install firmware-linux-nonfree

安装后需要重新配置并编译内核，在设置驱动的图形支持编译选项中需要指定相应的firmware路径与真正的firmware文件。

	Device Drivers --->
	  Generic Driver Options --->
	  [*]  Include in-kernel firmware blobs in kernel binary
	  # RadeonHD 2000, 3000, and 4000 series cards:
	  (radeon/R600_rlc.bin radeon/R700_rlc.bin) External firmware blobs

经过漫长的编译等待后，重新安装编译得到的内核，但是使用新内核启动依旧不能启动X界面。查看日志文件，会看到类似下面的启动错误：

	(EE) RADEON(0): [dri] RADEONDRIGetVersion failed because of a version mismatch.
	[dri] This chipset requires a kernel module version of 1.17.0,
	[dri] but the kernel reports a version of 2.0.0.[dri] If using legacy modesetting, upgrade your kernel.
	[dri] If using kernel modesetting, make sure your module is
	[dri] loaded prior to starting X, and that this driver was built
	[dri] with support for KMS.
	[dri] Disabling DRI

Google得之，该错误是由于KMS引起的，可以参考[Xorg Wiki](http://www.x.org/wiki/radeonBuildHowTo)。为了避免该错误，在Xorg Wiki中给出几种解决办法，但是在我的Debian上，配置文件内核启动命令行均不起作用，没有办法，只能最后在内核中进行配置了：

	(Enable Radeon KMS support)
	Device Drivers --->
	  Graphics support --->
	  <*>  Direct Rendering Manager --->
	  <*>    ATI Radeon
	  [*]      Enable modesetting on radeon by default

为了使内核支持KMS，我们还需要进行另外的相关配置：

	Device Drivers --->
	  Graphics support --->
	    Support for frame buffer devices --->
	    (Disable all drivers, including VGA, Intel, nVidia, and ATI)

	    (Further down, enable basic console support. KMS uses this.)
	    Console display driver support --->
	      <*>  Framebuffer Console Support

重新编译安装内核，使用新内核启动后终于看到久违的X界面了。具体请参考[Gentoo文档](http://www.gentoo.org/doc/en/xorg-config.xml)

That's all～～ Enjoy it～～
