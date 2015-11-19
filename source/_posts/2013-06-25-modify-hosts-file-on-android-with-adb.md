title: 使用adb工具修改Android上的hosts文件
date: 2013-06-25 21:53
category: 
- Android
tags: 
- android
- adb
- hosts
---

国内的网络环境很差，很多的国外网站都没有办法访问，如[Facebook](https://www.facebook.com)，[Twitter](https://twitter.com)，而[Google](https://www.google.com)的部分网站虽说可以访问，但是很多服务要么无法使用，要么不能稳定使用。为了能够稳定的访问所需要的网站，修改机器上的hosts文件是一种有效的办法，而对于Android手机，我们也可以通过修改其上的hosts文件实现对某些网站的访问。在这里推荐[一篇文章](http://irising.me/2011/08/9691)，有兴趣的朋友可以一试。

简而言之，方法步骤如下：

* 获取手机Root权限
* 通过数据线将手机与电脑相连，并打开Android手机的调试模式
* 获取Root权限`adb root`
* 重新挂载，获取读写权限`adb remount`
* 将手机上的hosts文件复制到电脑上`adb pull /system/etc/hosts`
* 修改hosts文件
* 将修改后的hosts文件复制到手机上`adb push hosts /system/etc/hosts`

当然，在上面所提到的文章中还介绍了其他的方法，有兴趣的朋友可以尝试！！
