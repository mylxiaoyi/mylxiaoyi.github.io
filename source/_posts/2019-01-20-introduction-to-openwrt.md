title: 刷机OpenWrt
date: 2019-01-20 22:16:44
category:
- network
tags: 
- openwrt
- network
---

[OpenWrt](https://openwrt.org/)作为一款优秀的开源路由系统，在为路由玩家提供多种新奇玩法的同时，也为借助智能路由器创业的年轻人提供了新的机遇。如果不想直接使用各种所谓的智能路由器，大可以自己购买支持刷机OpenWrt的路由器，自己动手折腾来打造一款满足自身需求的智能路由器。

本人并不是一名路由器玩家，确切地说，自己还只是一个路由器小白，仅是为了替换掉自己的TP-Link，同时尝鲜一下OpenWrt，因而由万能的淘宝上购买了一个二手的美国网件（NetGear)路由器，准备尝试自己刷机OpenWrt。

NetGear到手后，连接上电源，登陆进入管理界面，见识到了NetGear的原厂固件，转念一想，那就先试一下原厂固件吧。于是简单的配置后，就通地原厂固件来网上冲浪了。

我风买的这个二手NetGear带有2.4G和5G两个频段的无线接口。不过使用一段时间后发现，两个频段的无线接口都会出现手机可以连接无线网，但是无线网无法连接外网的情况，有时换着连一连，运气好的时候可以连通外网。最终让我痛下决定刷机的理由是，我的Kindle只能连接2.4G的无线网，无法连通外网，这就意味着无法接收亚马逊推送的电子书。

既然购买NetGear的初衷就是为了刷机OpenWrt，那为什么还要忍受原厂的固件呢？说刷就刷，于是体验到了下面的刷机流程。

首先到[OpenWrt官网](https://openwrt.org/toh/start)确定手中的路由器是否为OpenWrt所支持。相信你手中的路由器肯定是为OpenWrt所支持的，因为就是为了OpenWrt才买的路由器嘛。

然后可以由[OpenWrt的文档](https://openwrt.org/docs/guide-quick-start/begin_here)开始。重点是[这里](https://openwrt.org/docs/guide-quick-start/factory_installation)，在这里描述了刷机的详细步骤。

到[固件下载页面](https://openwrt.org/toh/views/toh_fwdownload)下载所需要的固件，将所下载的固件存放在本地文件夹中。

通过浏览器登陆路由器，所需要的用户名和密码通常都会在路由器机身找到。登陆后进入路由器固件管理页面，找到固件刷机菜单，上传先前所下载的固件，开始刷机。耐心等待路由器重启后，即可开始体验OpenWrt。

刷机后的操作可以参考[这里](https://openwrt.org/docs/guide-quick-start/walkthrough_login)。

首次登陆OpenWrt会看到类似如下的登陆的登陆界面

![OpenWrt登陆界面](/images/openwrt_login.png)

当然，在第一次登陆时会提醒设置root密码。为了安全起见，请妥善设置root密码。

登陆后可以看到系统概览以及各种可以设置的功能菜单。

对于我们而言，刷机后的首要操作就是连接网络了。不过相信初次接触OpenWrt的朋友会对OpenWrt的各种接口感到迷惑。当然对于熟悉Linux的朋友来说，eth0、eth1这样的名称就显得很是面熟了，这正是Linux系统中所用的网卡的名称了。对于脱胎于Linux的OpenWrt而言，也是同样如此。对于OpenWrt中的接品名称可以参考[这里](https://openwrt.org/docs/guide-developer/networking/network.interfaces)。

OpenWrt默认的WAN口设置并不是用于拨号上网的，因而需要我们自己设置。在`Network->Interfaces`页面将默认的WAN删除。点击`Add new interface...`按钮，会进入到创建新接口的页面，在这里填写所需要的信息。接口名字填写`WAN`，由于是用于拨号上网，因而接口协议选择`PPPoE`，所用的网卡为`eth1`。如下图所示。

![添加WAN口](/images/openwrt_add_wan.png)

提交之后即可进入详细设置页面，在这里填写我们宽带的用户名与密码。在这里需要提醒大家的是一定要设置WAN口的防火墙，否则会出现无法上网的情况。如下图所示。

![WAN防火墙](/images/openwrt_wan_firewall.png)

经过上面的设置后，如果一切正常，我们应该可以畅快地开始网络冲浪了。当然，OpenWrt的玩法还有很多，这需要我们自己去折腾探索。如果因为操作错误导致无法连接路由器的情况时，可以通过长按`Reset`按钮来恢复出厂设置。当然，在刷机了OpenWrt后，恢复出厂设置是指恢复OpenWrt的默认设置。

That'all~~ Enjoy it~~