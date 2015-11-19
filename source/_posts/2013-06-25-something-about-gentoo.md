title: Gentoo小记
date: 2013-06-25 15:35
categories: 
- Linux
tags: 
- linux
- gentoo
---

元旦三天假，重新折腾了一下机器上的Gentoo，在这里记录一下所遇到的问题，一是记录，二是希望能够对遇到相同问题的朋友有所帮助。

Gentoo的官网提供了详细的文档，由基本的系统安装到X与桌面环境的安装配置都提供有详尽的文档说明，从而为Gentoo新手提供了极大的帮助。只要我们参考这些文档，基本上就可以完成Gentoo系统的安装。但是在安装过程，有以下几个需要注意的问题：

##内核编译

Gentoo基系统，X以及ALSA的安装都有相关的内核编译说明，为了避免一次一次的重新编译内核，建议在配置内核时同时参考一下[X配置](http://www.gentoo.org/doc/en/xorg-config.xml)与[ALSA配置](http://www.gentoo.org/doc/en/alsa-guide.xml)部分的相关内容进行内核配置，特别需要注意的是，ALSA需要编译为内核模块，而不能直接编译进入内核，否则后面使用alsaconf进行配置时会出现"no supported pnp or pci card found"的错误。

##安装X

如果我们在编译安装内核时并没有配置X的相关配置选项，那么我们就需要参考[X配置](http://www.gentoo.org/doc/en/xorg-config.xml)进行相关的配置并重新编译内核。

我们可以通过命令

	emerge xorg-server

来安装X。但是这样会安装上许多我们根本就不需要的组件，因而我们需要有选择的进行安装。个人理解，对X组件的选择主要体现在为对X驱动的选择。我们可以通过/etc/portage/make.conf来指定需要安装的X驱动：

	# These settings were set by the catalyst build script that automatically
	# built this stage.
	# Please consult /usr/share/portage/config/make.conf.example for a more
	# detailed example.
	CFLAGS="-O2 -march=i686 -pipe"
	CXXFLAGS="${CFLAGS}"
	# WARNING: Changing your CHOST is not something that should be done lightly.
	# Please consult http://www.gentoo.org/doc/en/change-chost.xml before changing.
	CHOST="i686-pc-linux-gnu"
	# These are the USE flags that were used in addition to what is provided by the
	# profile used for building.
	USE="bindist"
	SYNC="rsync://mirrors.163.com/gentoo-portage"
	GENTOO_MIRRORS="http://mirrors.163.com/gentoo/"

	INPUT_DEVICES="evdev keyboard mouse synaptics"
	VIDEO_CARDS="radeon"

这样我们再运行

	emerge -pv xorg-server

时就会看到所需要安装的软件包大大减少。

现在X已经不需要Xorg.conf配置文件了，我们可以直接运行startx来测试一下我们的X是否安装成功。

##安装URxvt

URxvt，也就是所谓的rxvt-unicode，rxvt的unicode版本。在[Gentoo Wiki](http://en.gentoo-wiki.com/wiki/Rxvt-Unicode)中有关于URxvt安装配置的说明。在这里需要提醒注意的是在安装rxvt-unicode时要打开xft选项，否则编译出来的URxvt会出现不能正常显示而显示方框的现象。

	USE="truetype perl afterimage iso14755 256-color xft" emerge x11-terms/rxvt-unicode

##配置ALSA

在Gentoo官方文档[ALSA配置](http://www.gentoo.org/doc/en/alsa-guide.xml)中对ALSA的配置进行了说明。但是多次失败后发现，ALSA应作为内核块进行编译，而不能直接编译进入内核，然后才可以使用alsaconf进行配置。

##安装GVim

Gentoo默认情况下所安装的GVim界面很是丑陋，因而我们在进行编译安装时应打开gtk选项。

	USE="gtk" emerge gvim

##安装SCIM

SCIM的安装应注意的是需要在make.conf指定LIGUAS="zh_CN zh"，这样在安装scim-tables后才会有五笔输入法可用。

安装完成后，我们可以在.xinitrc中进行配置：

	export LANG=zh_CN.UTF-8
	export LANGUAGE=zh_CN.UTF-8
	export XMODIFIERS="@im=scim"
	export XIM=scim
	export XIM_PROGRAM=scim
	export GTK_IM_MODULE=scim
	export QT_IM_MODULE=scim
	scim &
	exec startfluxbox

##安装RVM

在使用RVM安装iconv时也许会遇到下面的错误：

	% rvm pkg install iconv
	Fetching libiconv-1.13.1.tar.gz to /pub/home/rvm/.rvm/archives
	  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
					 Dload  Upload   Total   Spent    Left  Speed
	100 4605k  100 4605k    0     0   269k      0  0:00:17  0:00:17 --:--:--  340k
	Extracting libiconv-1.13.1.tar.gz to /pub/home/rvm/.rvm/src
	Prepare libiconv in /pub/home/rvm/.rvm/src/libiconv-1.13.1.
	ERROR: Error running 'autoreconf -is --force', please read /pub/home/rvm/.rvm/log/libiconv/autoreconf.log
	Configuring libiconv in /pub/home/rvm/.rvm/src/libiconv-1.13.1.
	ERROR: Error running ' ./configure --prefix="/pub/home/rvm/.rvm/usr"  ', please read /pub/home/rvm/.rvm/log/libiconv/configure.log
	Compiling libiconv in /pub/home/rvm/.rvm/src/libiconv-1.13.1.
	ERROR: Error running 'make ', please read /pub/home/rvm/.rvm/log/libiconv/make.log
	Installing libiconv to /pub/home/rvm/.rvm/usr
	ERROR: Error running 'make install', please read /pub/home/rvm/.rvm/log/libiconv/make.install.log

解决方法请参考[RVM官网](https://rvm.io/packages/iconv/)以及[github讨论](https://github.com/wayneeseguin/rvm/issues/614)。即：

	rvm --skip-autoreconf pkg install iconv

##使用Octopress

使用Octopress撰写博文后生成静态网页时也许会遇到下面的错误：

	mylxiaoyi@mybox ~/octopress $ rake generate
	## Generating Site with Jekyll
	unchanged sass/screen.scss
	/home/mylxiaoyi/.rvm/gems/ruby-1.9.3-p362/gems/maruku-0.6.0/lib/maruku/input/parse_doc.rb:22:in `<top (required)>': iconv will be deprecated in the future, use String#encode instead.
	Configuration from /home/mylxiaoyi/octopress/_config.yml
	Building site: source -> public
	/home/mylxiaoyi/.rvm/gems/ruby-1.9.3-p362/gems/jekyll-0.11.2/lib/jekyll/convertible.rb:29:in `read_yaml': invalid byte sequence in UTF-8 (ArgumentError)
		from /home/mylxiaoyi/.rvm/gems/ruby-1.9.3-p362/gems/jekyll-0.11.2/lib/jekyll/post.rb:39:in `initialize'
		from /home/mylxiaoyi/octopress/plugins/preview_unpublished.rb:23:in `new'
		from /home/mylxiaoyi/octopress/plugins/preview_unpublished.rb:23:in `block in read_posts'
		from /home/mylxiaoyi/octopress/plugins/preview_unpublished.rb:21:in `each'
		from /home/mylxiaoyi/octopress/plugins/preview_unpublished.rb:21:in `read_posts'
		from /home/mylxiaoyi/.rvm/gems/ruby-1.9.3-p362/gems/jekyll-0.11.2/lib/jekyll/site.rb:128:in `read_directories'
		from /home/mylxiaoyi/.rvm/gems/ruby-1.9.3-p362/gems/jekyll-0.11.2/lib/jekyll/site.rb:98:in `read'
		from /home/mylxiaoyi/.rvm/gems/ruby-1.9.3-p362/gems/jekyll-0.11.2/lib/jekyll/site.rb:38:in `process'
		from /home/mylxiaoyi/.rvm/gems/ruby-1.9.3-p362/gems/jekyll-0.11.2/bin/jekyll:250:in `<top (required)>'
		from /home/mylxiaoyi/.rvm/gems/ruby-1.9.3-p362/bin/jekyll:19:in `load'
		from /home/mylxiaoyi/.rvm/gems/ruby-1.9.3-p362/bin/jekyll:19:in `<main>'
		from /home/mylxiaoyi/.rvm/gems/ruby-1.9.3-p362/bin/ruby_noexec_wrapper:14:in `eval'
		from /home/mylxiaoyi/.rvm/gems/ruby-1.9.3-p362/bin/ruby_noexec_wrapper:14:in `<main>'

关于该问题的讨论，可以参看https://github.com/imathis/octopress/issues/267与https://github.com/imathis/octopress/issues/144。讨论的结果就是要设置系统的locale。但是看到大家的讨论结果实在是觉得有些迷惑。也许是因为大家都在使用Mac并且英文的情况吧，对于这些需要使用中文输入法的用户来说，将LANG设置为en_US.UTF-8就没有办法使用中文输入法了。所以个人觉得问题的关键似乎并不在这里。对于我个人来说，问题在于新写的文章没有使用UTF-8进行保存，保存为UTF-8编码后问题解决。Fait！！

另外推荐[这里](http://jokry.com/2012/03/08/octopress/)关于Octopress安装与配置的介绍，相信对大家会有一些帮助。

最后不得不提的，Gentoo官方文档实在是相当的丰富与详细，多多阅读Gentoo官方文档一定会收获良多。

That's all～～Enjoy it～～
