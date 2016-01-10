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

## Emacs

要使得Emacs支持字体设置，我们需要在编译时启用`xft`：

```bash
USE="xft" emerge -av emacs
```
