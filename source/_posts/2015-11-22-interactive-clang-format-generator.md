title: 交互式clang-format生成器
date: 2015-11-22 22:35:30
categories:
- C++
tags:
- linux
- c++
---

[clangformat.com](http://clangformat.com/)是一个通过交互方式设置clang-format格式选项并查看格式化效果的网站，将所生成的配置选项命名为`.clang-format`并保存在用户主目录下，然后在使用`clang-fromat`格式化代码就会读取并应用该配置选项。但是在这里需要注意的一点就是，`PointerBindsToType: 100`这一句是该网站的默认选项，但是右侧的解释部分我们可以清楚地看到，该选项的值是一个`bool`类型值，因而该选项的值应为`true`或是`false`。我们在保存文件时应根据我们的实际需要修改。

这样就可以使用`clang-format`来格式化生成漂亮的代码了。

什么？没有`clang-format`？如果在[Debian](http://debian.org)系统，可以使用超级牛力的`apt-get`来安装：

```
apt-get install clang-format-3.5
```
