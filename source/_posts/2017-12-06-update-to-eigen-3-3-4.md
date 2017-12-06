title: 升级 eigen3.3.4
date: 2017-12-06 22:33:29
tags: eigen
---

[Eigen](http://eigen.tuxfamily.org/index.php?title=Main_Page) 作为大名鼎鼎的代数计算库，为许多计算软件库所依赖，而在开发过程中由于Eigen库所引起的编译错误也着实让人光火。

在我的Debian9下，GCC版本为6.3.0，默认的ABI为C++14。而在编译使用Eigen的软件包时，由于某些废弃的C++11语法会导致大量的编译警告，虽说可以通过GCC的编译选项来关闭这些警告，但还是让人心里很不舒畅。在最近更是遇到了由于Eigen中StdVector所导致的`partial specialization`问题则更是让人不知所措。在一番[Google](https://www.google.co.uk)之后，找到[关于该问题的讨论](http://eigen.tuxfamily.org/bz/show_bug.cgi?id=829)。原来是由于C++11中vector接口的变化所导致的。

正在郁闷时，点开Eigen的主页，看到已发布3.3.4版本，抱着试试看的态度下载编译安装，再重新编译之前的软件，居然顺利通过了，并且也不再有恼人的C++11的废弃编译警告了。

如果你也遇到类似的问题，不妨升级到最新的3.3.4版本来试试吧。