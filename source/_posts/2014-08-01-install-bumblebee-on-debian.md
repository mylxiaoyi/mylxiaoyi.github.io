title: 不作不死－Debian折腾记
date: 2014-08-01 22:17
categories: 
- Linux
---

时间飞逝，转眼之间距离上一篇《不作不死-Gentoo折腾记》已经过去近两个月的时间了。事实上，Debian的折腾与前面Windows和Gentoo的折腾是同时的，然而由于各种原因一直没有记录，今天在这里记录一下。

我个人的Debian使用历史还要从大学时代说起。那个时候刚刚买了电脑，由于个人的兴趣也开始尝试折腾各种Linux发行版，可以说那个时候每天的主要折腾内容就是重装Linux。当时学校里论坛里有一个大牛，他极为推崇Debian，并且据他说，自从他用了Debian以后就再也没有换过其他的Linux发行版本。于是在大牛的指引，我也开始了Debian的尝试，当然也正始他所说，我使用了Debian以后也不再想换用其他的Linux发行版本了。丰富的软件源，超级牛力的apt-get，这就是Debianer深爱的原因。正如网友所说，使用Debian就像开了一家超市，无论缺什么，只需要apt-get就可以了。当然，Debian为了追求系统的稳定性，软件的陈旧一直为大家所诟病，而随着基于Debian的Ubuntu的兴起，Debian也加快了开发的节奏。当然，对于个人用户来说，使用testing源，或者sid源或是experimental源，软件包的版本还是相当新，而软件包的稳定性也并不如听起来的那样恐怖。

好了，说了这么多无非是想要表达一下个人对于Debian的喜爱。在前面的文章中我们提到过，折腾Debian的原因就是为了能够使用CUDA进行GPU加速以及编译OpenCV。然而不幸的是，由于Nvidia对于Linux的支持问题，Nvidia所提出的Optimus技术是我们在Linux下使用CUDA的主要障碍。如果我们的笔记本支持在Bios里关掉集成显卡从而仅使用独立的Nvidia显卡，那是最好不过的了。但是在很多时候，我们所选购的笔记本提供商并不会在Bios里提供这样的功能，那么我们就需要转求其他的解决方案了。

幸运的是，伟大的开源运动为我们提供了Bumblebee解决方案，我们可以在其项目主页上看到相应的特性介绍以及安装方法。对于Bubmlebee在Debian下的安装，Debian Wiki介绍了相应的安装方法，这是我们首先要参考的文档。概括起来，Debian Wiki所提到的方法主要分为下面几步：

* 安装Nvidia驱动

关于Nvidia驱动的安装，我们可以在Nvidia的官网下载对应的驱动。当然，在Debian的安装源中也有相应的Nvidia驱动，我们也可以选择通过apt-get来进行安装。

我们可以使用下面的命令来搜索一下Debian源中的Nvidia驱动：

	apt-cache search nvidia

这样我们会看到多个Nvidia驱动可以选择，我们可以选择其中一个进行安装：

	apt-get install nvidia-driver

`apt-get`会自动为我们解决相关的依赖关系。

* 安装Bumblebee

同样我们也可以先来查看一下源中Bumblebee中的软件包情况：

	apt-cache search bumblebee

然后通过`apt-get`进行安装：

	apt-get install bumblebee bumblebee-nvidia

Bumblebee提供了optirun程序，我们可以通过该实用程序将相应的计算任务提交给GPU完成。例如，我们可以能过下面的命令将firefox程序放在GPU上运行：

	optirun firefox

如果一切顺利我们会看到firefox正常启动运行。然而一切顺利终归是最为理想的情况，实际的情况并非总是如此，我们也许会遇到`无法访问nvidia驱动`这样的错误，解决方法是编辑`/etc/bumblebee/bumblebee.conf`，将`KernelDriver=nvidia`改为`KernelDriver=nvidia-current`，保存后重启`bumblebeed`守护进程：

	/etc/init.d/bumblebeed restart

这样以后我们应该就可以顺利启动firefox了，当然，很有可能我们需要多次运行该命令才会启动成功。

好了，关于Debian Wiki上所介绍的关于Bumblebee的安装大致就是如此了，但是实际中我们会发现，仅是这样的安装步骤是不足够的。

要测试Nvidia驱动以及Bumblbee是否安装成功并正常工作，我们可以使用一个简单的测试程序，即`mesa-utils`所提供的`glxgears`。首先我们运行一下`glxgears`来看一下集成显卡的渲染速度：

	glxgears

在我的机器上大致会达到60FPS这样的速度。

下面我们来测试一下在GPU上的渲染速度：

	optirun glxgears

很奇怪的结果不是吗？GPU的渲染速度和集成显卡的渲染速度居然是一样的？如果仅是这样的渲染速度，我们还折腾CUDA运算干什么呢？为什么会出现这样的结果呢？

一切的原因就在于Debian Wiki介绍中少了一步，那就是安装`virtualgl`。关于`virtualgl`，我们可以在其项目主页上看到相应的介绍，简单来说，`virtualgl`所要实现的就是将渲染操作放在GPU上运行，而将渲染结果显示放在集成显卡上运行。这样看来`virtualgl`正是Bumblebee所需要的。

我们可以在`virtualgl`的项目主页上循着相应的链接下载安装`virtualgl`，安装后我们再运行下面的命令：

	optirun glxgears

我们就会惊喜地看到，GPU的渲染速度可以惊艳地达到1800FPS，这才是GPU运行应有的水准嘛。

好了，我们的安装步骤已经接近尾声了，但是我们注意到，上面一系列的命令操作都是以`root`用户进行的，而当我们普通用户运行上面的命令时却告诉权限不足，并提示我们将用户加入`bumblebee`组，我们可以通过下面的命令来添加：

	gpasswd -a user bumblebee

注销后再重新登陆，我们就可以顺利运行`optirun`命令了。

搞了Nvidia驱动与Bumblebee，安装CUDA开发包就是很简单的事情了。首先我们可以看一下源中所提供的cuda开发包：

	apt-cache search cuda

然后安装相应的软件包：

	apt-get install nvidia-cuda-dev nvidia-cuda-toolkit

`apt-get`会自动为解决依赖关系并安装相应的依赖包。

以上就是我在Debian上折腾CUDA的经历，准备好了CUDA开发环境，下面我们就可以学习CUDA开发了。
