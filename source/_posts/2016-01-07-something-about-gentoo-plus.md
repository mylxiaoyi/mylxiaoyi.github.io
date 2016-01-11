title: Gentoo安装补记
date: 2016-01-07 22:52:01
categories:
- Linux
tags:
- linux
- gentoo
---

Gentoo安装成功后，在后续的使用过程中还会涉及到一些软件的安装与配置问题，在这里补记一下。

## WebCamera

要想使用笔记本自身携带的摄像头或是外接USB摄像头，我们需要配置内核使其支持WebCamera。

```bash
Device Drivers --->
    <M> Multimedia support --->
        [*] Cameras/video grabbers support
        [*] Media Controller API
        [*] V4L2 sub-device userspace API
        [*] V4L platform devices
```

## USB3

```bash
Device Drivers --->
    [*] USB support --->
    [M] xHCI HCD (USB 3.0) support
```

## ALSA

再次证实，虽然在Gentoo官方文档[ALSA配置](http://www.gentoo.org/doc/en/alsa-guide.xml)中对ALSA的配置进行了说明。但只有将ALSA编译为内核模块，而不能直接编译进入内核，然后才可以使用alsaconf进行配置。

## Emacs

要使得Emacs支持字体设置，我们需要在编译时启用`xft`：

```bash
USE="xft" emerge -av emacs
```

## QtDoc

在Gentoo下做Qt开发，Qt的参考文档是必不可少的工具：

```bash
emerge -av dev-qt/qt-docs
```

而`QtCreator`作为一款优秀的开发工具，当仁不让的成为Qt开发工具的不二之选，然而为了能够在QtCreator中显示漂亮的Qt参考文档，我们需要打开`webkit`：

```bash
 USE="webkit doc" emerge -av dev-qt/qt-creator
```

如果在启动QtCreator之后点击Help之后显示空页面，则需要在`Options > Help > Documentation`设置页面添加`/usr/share/doc/qt-5.5.1`目录下所有的QCH文件。

## Lighttpd

架设一个私人服务器，将自己常用的一些库的文档放在服务器目录下，在需要的时候通过浏览器浏览查看，可以为我们平时的工作与学习带来极大的方便。`lighttpd`轻量、小巧，足够我们平时的个人使用：

```bash
emerge -av www-servers/lighttpd
```

配合PHP使用需要进行简单的配置，而且所需要的配置十分简单：

    vim /etc/lighttpd/lighttpd.conf

在配置文件的最后部分，启用`mod_fastcgi`:

    include "mod_fastcgi.conf"

编辑`mod_fastcgi.conf`文件，在其中指定`php-cgi`的路径：

    “bin-path" => "/usr/bin/php-cgi"


## PHP

需要启用"cgi"选项，当然也需要根据我们的需要启用各个选项，例如数据库操作相关的选项：

    USE="cgi" emerge -av dev-lang/php
