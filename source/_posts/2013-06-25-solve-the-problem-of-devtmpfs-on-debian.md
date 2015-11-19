title: 解决Debian上的devtmpfs问题
date: 2013-06-25 17:33
categories: 
- Linux
tags: 
- debian
- devtmpfs
---

其实也不单是Debian上存在这样的问题，相信在其他的发行版本中也会存在，因为问题的根源在于内核的配置。具体的表现为在Debian启动时会出现类似如下的警告：

> devtmpfs is not fully populated on /dev

虽然这样的警告并不会影响系统正常的开机与使用，但是每天在开机启动时都会看到这样一条警告信息毕竟会觉得不爽，Google一下，可以找到如下关于[devtmpfs](http://wiki.linux.org.hk/w/Devtmpfs)的说明：

> devtmpfs 的功用是在 Linux 核心 啟動早期建立一個初步的 /dev，令一般啟動程式不用等待 udev，縮短 GNU/Linux 的開機時間。devtmpfs 在 2009 年初被提出，並在同年年尾進出的 Linux 2.6.32 正式收錄。

另外在[这里](http://my.oschina.net/u/158589/blog/78569)与[这里](http://lwn.net/Articles/330985/)也有关于devtmpfs的介绍与说明。

知道了devtmpfs是什么，那么我们就可以在内核配置中加入对devtmpfs的支持，在.config配置文件中加入：

	CONFIG_DEVTMPFS=y
	CONFIG_DEVTMPFS_MOUNT=y

或是使用`make menuconfig`：

	Device Drivers  --->
	    Generic Driver Options  --->
		[*] Maintain a devtmpfs filesystem to mount at /dev 
		[*]   Automount devtmpfs at /dev, after the kernel mounted the root

保存配置后重新编译安装新内核，并使用新内核启动，我们就会发现，之前的警告信息不见了！！！

That's all～～Enjoy it～～

PS：在经过一番更新后，我的Fcitx终于恢复正常了！！！
