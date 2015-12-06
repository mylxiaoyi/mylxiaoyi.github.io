title: Sublime Text中的包管理
date: 2015-12-06 18:23:39
categories:
- SublimeText
tags:
- sublime text
---

[Sublime Text](http://www.sublimetext.com/)是一个优秀的文本编辑器，通过各种插件可以极大地扩展其自身的功能，从而可以方便地将其打造为得心应手的开发利器，而[Package Control](https://packagecontrol.io/)正是Sublime Text强大功能的基石。

当然，我们首先要做的就是为Sublime Text安装Package Control，在[Package Control的官网](https://packagecontrol.io/installation)上介绍了具体的安装命令，对于Sublime Text3而言，我们需要通过`Ctrl+'`，或是菜单项中的`View->Show Console`来打开Sublime Text控制台，然后输入如下命令：

``` bash
import urllib.request,os,hashlib; h = '2915d1851351e5ee549c20394736b442' + '8bc59f460fa1548d1514676163dafc88'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```

重新启动Sublime Text后就可以通过`ctrl+shift+p`来搜索并安装自己所需要的插件了。

不知道有哪些插件？没有关系，可以在[Package Control](https://packagecontrol.io/)浏览搜索自己所需要的插件哦。