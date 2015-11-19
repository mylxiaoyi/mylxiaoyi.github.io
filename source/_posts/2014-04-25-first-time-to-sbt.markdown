title: 初识sbt
date: 2014-04-25 19:37:28
categories:
- scala
tags: 
- scala
- sbt
- coursera
---

最近在[Cousera](https://coursera.org)上开始了一门介绍使用[Scala](http://www.scala-lang.org)进行函数式编程的公开课[Functional Programming Principles in Scala](https://class.coursera.org/progfun-004)，该课程使用基于[Eclipse](http://www.eclipse.org)的[ScalaIDE](http://scala-ide.org)作为开发工具，使用[SBT](http://http://www.scala-sbt.org)作为构建工具，从而第一次知道了SBT这个构建工具。

关于课程所用开发工具及环境的搭建，在[课程视频](https://class.coursera.org/progfun-004/lecture)有详细的介绍，而且在[Tools Setup](https://class.coursera.org/progfun-004/wiki/ToolsSetup)还有相应的说明，而关于SBT的初步使用在[Sbt Tutorial](https://class.coursera.org/progfun-004/wiki/SbtTutorial)部分也有相应的介绍，通过阅读这些说明，我们就可以初步了解Sbt的使用，以满足该课程的需要。当然，如果我们感兴趣或是有需要，我们可以网络上找到大量的相关文档，当然[官网的文档](http://www.scala-sbt.org/0.12.2/docs/index.html)是我们一定要找机会细读的，另外在[Github](https://github.com)上还有一份相当不错的[关于Sbt的介绍](http://twitter.github.io/scala_school/zh_cn/sbt.html)。

然而在实际使用时，我们就会发现，Sbt的自动化库管理工具需要连接网络下载相关的依赖包，而且似乎是每执行一个任务就要连网下载，完全没有理会已下载的JAR包（很有可能是我没有正确的理解Sbt才会有这样的认识），对于我们这样糟糕的网络环境来说，这绝对是一个极大的挑战，即使是在早上人少网络环境相发不错的时候连网下载也不见得会成功，这对于刚刚燃起学习兴趣的我来说是一个极大的打击。

不过好在这个问题并不是不能解决的。在[Google](https://google.com.hk)上搜索一下，我们就可以找到大量的关于Sbt库依赖管理的内容，当然在Sbt官网文档的[库管理部分](http://www.scala-sbt.org/0.12.2/docs/Getting-Started/Library-Dependencies.html)就已经介绍了Sbt的依赖库管理方法。原来在Sbt中，我们可以使用自动与手动两种方式来管理Sbt工程的依赖库，即Managed Dependencies与Unmanaged dependencies，当使用自动方式时，在工程目录下的`build.sbt`文件指定所依赖的库：

	libraryDependencies += "org.scalatest" %% "scalatest" % "1.9.1" % "test"

	libraryDependencies += "junit" % "junit" % "4.10" % "test"

上面的两行代码就指定了`scalatest-1.9.1`与`junit-4.10`两个依赖库。然而也就因为这两个依赖库，在执行Sbt任务时就会长时间卡在依赖库下载这一步。

幸好我们还可以选择使用手动管理依赖库。当使用手动管理依赖库时，我们只需要将所依赖的库放在工程目录下的`lib`目录中，当启动Sbt时，Sbt会自动将`lib`目录下的库文件添加到`classpath`中。但时在这里我们需要注意的一点就是，当选择使用手动管理方式时，我们需要将`build.sbt`文件中的上述两行代码注释掉，否则依然还是使用自动管理方式，依然会连网下载。

	//libraryDependencies += "org.scalatest" %% "scalatest" % "1.9.1" % "test"

	//libraryDependencies += "junit" % "junit" % "4.10" % "test"

这就是我在初次使用Sbt时遇到的问题，记录在这里，希望会对遇到类似情况的朋友有用。
