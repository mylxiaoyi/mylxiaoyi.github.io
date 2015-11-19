title: 不作不死－Gentoo折腾记
date: 2014-06-19 08:31
categories: 
- Linux
---

Gentoo，跑得最快的小企鹅，是Linux社区中一个独特的发行版本。采用与FreeBSD类似的Portage包管理机制，通过`emerge`自动化构建工具，采用由源码编译的方式构建整个系统，通过精确的编译选项控制，力求最大限度的压榨硬件性能，从而提供不同的Linux使用体验。正所谓成也`emerge`，败也`emerge`，`emerge`是Gentoo爱好者所津津乐道的自动化构建工具，为Gentoo聚集了大批忠实的粉丝，然而也正是`emerge`的高门槛，挡住了许多Gentoo初学者尝试的脚步。在网络上有一篇Gentoo创始人所写的关于Gentoo设计哲学及其发展历程的文章（Gentoo的前世今生），大家可以找来一读，相信在读后，大家对Gentoo会有更多的了解。

言归正传，在前面的一篇文章中提到，由于在Debian下编译安装OpenCV所遇到的困难以及某软件文档中提到的在Gentoo下的CUDA环境下进行测试的诱惑（CUDA正是我所需要的），最终毅然决定再重新折腾一次Gentoo（之所以说重新，是因为之前有过折腾Gentoo的经历）。

提到Gentoo的安装，对于许多Gentoo初学者来说也同样是一件很困难的事情。好在Gentoo官方提供了大量的文档可供参考，如果我们参考这些官方的安装文档，Gentoo的安装就会变得容易得多。

概括起来Gentoo的安装可以分为下面几步：

* 准备硬盘
* 解压stage3
* 解压portage
* 编译安装内核
* 编译安装必须的系统工具
* 编译安装grub启动引导
* 配置系统
* 重启进入Gentoo新系统
* emerge world

说起来就是这样简单的几步，但是实际操作起来，我们就会发现有许多需要注意的地方。首先我们需要在`/etc/portage/make.conf`中设置Gentoo安装源，在这里推荐163的源：

    GENTOO_MIRRORS="http://mirrors.163.com/gentoo/"

为了在编译安装软件包时不会安装无谓的语言包，我们可以在`/etc/portage/make.conf`中设置编译时所用的语言：

    LINGUAS="zh_CN"

在安装X时，我们需要指定要安装的驱动：

    INPUT_DEVICES="evdev keyboard mouse synaptics"
    VIDEO_CARDS="intel nvidia"

然后通过下面的命令安装X：

    emerge -av xorg-server
    
如果需要图形界面，我们可以选择轻量级的XFCE4：

    emerge -av xfce4-meta

当然，通过这种方式安装的软件会相对比较多，如果我们有耐心，或是想了解一下XFCE4的软件包组成，我们可以选择`emerge`单个软件包的方式来安装。在这里要提醒大家一下的就是，通过上面的命令所安装的软件包并未包含`xfce4-terminal`，如果我们希望使用该终端软件，一定要记得另外安装哦。

好了，这样我们就可以准备启动XFCE4了。当然在启动之前我们还需要最后一步配置：

    echo "exec startxfce4" >> .xinitrc

这样以后我们就可以启动X了：

    startx

一般来说，我们会比较顺利的进入XFCE图形环境。

在熟悉的图形环境里，我们就可以`emerge`整个世界了。

但是我折腾Gentoo的最初目的是为了cuda与opencv啊，如果不解决这两个软件包，那折腾Gentoo岂不是没有意义了？！在这里不得不感叹一下，Gentoo通过源码安装的方式确实在某种程度上带来了便利。

要安装cuda，首先要解决nvidia显卡驱动问题。在前面安装X时，我们通过指定VIDEO_CARDS已经顺利地安装了闭源nvidia驱动，我们所缺少就是切换显卡驱动的bumblebee：

    emerge bumblebee

为了测试显卡驱动是否安装成功，我们可以使用`mesa-utils`测试套件：

    emerge mesa-utils

首先我们可以看一下不使用nvidia驱动时的`glxgear`速度：
    
    glxgear

然后再对比一下使用nvidia驱动时的`glxgear`速度：
    
    optirun glxgear

此时我们应看到速度上的巨大差异。如果看到两者之间的速度差不多，那就需要检查一下我们的软件安装是否正确。

安装cuda开发工具包：

    emerge -av nvidia-cuda-toolkit

安装opencv软件包：

    emerge -av opencv

此时我们就会看到可以使用的各处USE FLAG，我们可以根据我们的需要打开或关闭相应的USE FLAG，而portage会自动为我们解决相应的依赖关系。

OK，一切就绪，前进吧！！
