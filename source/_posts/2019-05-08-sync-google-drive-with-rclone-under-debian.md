title: 在Debian系统下使用rclone同步Google Drive文件
date: 2019-05-08 20:43:51
tags:
- debian
- rclone
- google drive
categories:
- Linux
---

[Google Drive](https://drive.google.com)作为[Google](https://google.com)旗下的云盘服务，在互联网云盘服务相继关闭的今天，可谓硕果仅存的云盘服务提供商之一，是网络用户之间进行文件交换与共享以及个人文件云备份的重要工具之一。Google Drive仅为Windows与MAC系统提供了官方客户端，而在Linux系统下则需要选用第三方的同步工具。[rclone](https://rclone.org)作为一款优秀的开源同步工具，可以为Google Drive文件的同步提供良好支持。

# 安装

在[Debian](https://www.debian.org/)下可以使用具有超级牛力的APT工具进行安装

```bash
sudo apt install rclone
```

# 配置

首次同步Google Drive时需要对`rclone`进行简单的配置。运行命令

```bash
rclone config
```

后会得到如下的输出：

> 2019/05/08 21:17:11 Config file "/home/mylxiaoyi/.rclone.conf" not found - using defaults
No remotes found - make a new one
n) New remote
s) Set configuration password
q) Quit config
n/s/q> n
name> google-drive
Type of storage to configure.
Choose a number from below, or type in your own value
 1 / Amazon Drive
   \ "amazon cloud drive"
 2 / Amazon S3 (also Dreamhost, Ceph, Minio)
   \ "s3"
 3 / Backblaze B2
   \ "b2"
 4 / Dropbox
   \ "dropbox"
 5 / Encrypt/Decrypt a remote
   \ "crypt"
 6 / Google Cloud Storage (this is not Google Drive)
   \ "google cloud storage"
 7 / Google Drive
   \ "drive"
 8 / Hubic
   \ "hubic"
 9 / Local Disk
   \ "local"
10 / Microsoft OneDrive
   \ "onedrive"
11 / Openstack Swift (Rackspace Cloud Files, Memset Memstore, OVH)
   \ "swift"
12 / Yandex Disk
   \ "yandex"
Storage> 7
Google Application Client Id - leave blank normally.
client_id> 
Google Application Client Secret - leave blank normally.
client_secret> 
Remote config
Use auto config?
 * Say Y if not sure
 * Say N if you are working on a remote or headless machine or Y didn't work
y) Yes
n) No
y/n> 

在配置步骤的最后，如果我们的网络可以直接连接墙外，则可以选择`y) Yes`，`rclone`会自动打开一个浏览器窗口，在浏览器窗口中完成访问授权并获得Token；如果很不幸，你的网强同我一样不能直接连接墙外，则需要选择`n) No`，按照`rclone`的提示访问指定链接完成访问授权，将授权码告知`rclone`。

> If your browser doesn't open automatically go to the following link: https://accounts.google.com/o/oauth2/auth?client_id=202264815644.apps.googleusercontent.com&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob&response_type=code&scope=https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fdrive&state=c0c50b84e9ecb4e92d6bf8b46abbd4c7
Log in and authorize rclone for access
Enter verification code> 

这样就可以完成`rclone`的配置了。

# 使用

完成配置以后，就可以通过`rclone`来管理同步远端Google Drive上的文件了。

当运行命令

```bash
rclone
```

就可以看到`rclone`的使用方法了。

例如，可以查看远端云盘中的文件夹

```bash
rclone lsd google-drive:
```

查看远端云盘中的文件

```bash
rclone ls google-drive:
```

将远端云盘中的文件夹同步到本地

```bash
rclone sync google-drive:remote-dir local-dir
```

更为具体的使用方法可以参考`rclone`帮助或[官方文档](https://rclone.org/drive/)。

That's all~~ Enjoy it~~