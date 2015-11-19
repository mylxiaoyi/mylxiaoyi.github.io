title: 关于MySQL存储引擎的一点记录
date: 2014-03-01 09:48
categories: 
- MySQL
tags: 
- mysql
- myisam
- innodb
---

## 问题源起

之前曾经尝试使用[Wikimedia](http://wikimedia.org)来记录自己学习时的文档，而Wikimedia使用MySQL数据库作为后台存储工具，所以在自己搭建的Wikimedia上所写的所有东西都是存储在MySQL数据库中。后来在朋友的推荐下开始尝试使用[Sphinx-doc](http://sphinx-doc.org)来管理自己的文档，并且一直想关要将以前Wikimedia中的内容也全部转移到Sphinx-doc上来，由于自己的懒惰一直没有完成这一工作。

然而在换了一个新的工作环境后，首先想到的就是要将以前的工作记录全部迁移过来，Sphinx-doc的文本文档很简单，只需要将文本文件拷贝过来就可以了。当然对于这样的MySQL数据库的备份与导入也是很简单的，然而自己犯懒，只是简单的将MySQL安装目录下data子目录中相应的数据为目录拷贝过来，结果导致拷贝过来的数据库文件不能在MySQL数据库中打开。这是为什么呢？

在网上Google一阵后了解到，采用InnoDB存储引擎存储的数据库文件在简单的数据库文件拷贝后就会出现这样的问题。因为真正的数据文件是存储在data子目录下的ibdata1文件中的，如果我们细心看一下就会发现，这个文件的体积是相当大的。由于没有将该文件一同拷贝过来，丢失了真正的数据文件，从而导致了不能查看数据的问题。

## 问题解决

知道了问题的原因，问题的解决就相对简单多了。委托同事将ibdata1文件拷贝出来放在新的MySQL安装目录中，然后在phpMyAdmin中查看数据库，此时数据库文件已经活生生的存在那里了。

问题是解决了，那MyISAM与InnoDB两个存储引擎之间到底有怎样的区别呢？现将搜索的一些结果记录如下。

## MyISAM与InnoDB的区别与对比

如果在[Google](https://www.google.com.hk/#newwindow=1&q=myisam+innodb&safe=strict)搜索一下，我们就会得到一系列的关于两者的对比分析：

MySQL 5.5关于MyISAM与InnoDB两种存储引擎的文档描述：

* [MyISAM](http://dev.mysql.com/doc/refman/5.5/en/myisam-storage-engine.html)
* [InnoDB](http://dev.mysql.com/doc/refman/5.5/en/innodb-storage-engine.html)

关于MyISAM与InnoDB性能的简单评测：[http://blog.csdn.net/fu_zk/article/details/10477113](http://blog.csdn.net/fu_zk/article/details/10477113)

[酷壳](http://coolshell.cn/)关于MyISAM与InnoDB选择的一些思考：[http://coolshell.cn/articles/652.html](http://coolshell.cn/articles/652.html)

## 经验教训

总结一下所学到的经验教训，那就是：

* 使用mysqldump工具进行MySQL数据库的备份与恢复，而不要简单的备份数据库文件；
* 如果只是简单的拷贝数据库文件，且该数据库使用InnoDB存储引擎时，一定要记得同时拷贝ibdata1文件。

That's all~~ Enjoy it~~
