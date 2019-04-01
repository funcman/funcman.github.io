---
layout: post
title:  "另一种在Windows10下使用Jekyll的方法"
date:   2019-03-31 08:37:45
categories: jekyll
---
最近准备继续记博客，需要安装Jekyll环境。

手头既有Mac也有PC，考虑到上班期间带的是PC，所以还是要在Windows下配置一个Jekyll环境。

按照Jekyll官网的方法，是去安装RubyInstaller。但是，RubyInstaller本身是基于MSYS2的，我的PC已经装了MSYS2，不想再多搞一套，于是决定基于MSYS2安装Ruby和Jekyll。

MSYS2类似Cygwin（其实就是一个Cygwin分支），可以让用户在Windows下使用UNIX环境的套件。这里先介绍一下相关概念：

* Pacman是一套流行于Arch Linux下的软件包管理软件。

* MinGW是一套在Windows使用的GNU工具链，MinGW-w64是新一代MinGW，同时支持32位和64位。

* MSYS是为帮助MinGW在Windows下使用，随MinGW提供的一套基本的POSIX操作环境。它是基于较旧的Cygwin项目创建的一个分支。MSYS2和MSYS并不是同一个项目的不同版本，它们各为独立项目。

MSYS2集成了Pacman和MinGW-w64。

我们在中国使用MSYS2，最好设置一下国内的镜像，网上的文章基本都是让用[中科大的镜像](https://lug.ustc.edu.cn/wiki/mirrors/help/msys2)，但目前他们的东西有点问题，只好改用[清华的镜像](https://mirrors.tuna.tsinghua.edu.cn/help/msys2)。可惜了，实际上中科大离我很近。

按照页面上的说明设置好镜像，并执行：

```
pacman -Syu
```

在最后提示关闭MSYS2时，点窗口的X进行关闭，再去MSYS2安装目录里运行一下autorebase.bat。然后打开MSYS2，执行一下更新：

```
pacman -Su
```

之后就安装一系列软件，我这里是：

```
pacman -S vim git base-devel mingw-w64-x86_64-toolchain mingw-w64-x86_64-ruby mingw-w64-x86_64-ruby-native-package-installer
```

之所以是使用mingw64/mingw-w64-x86_64-ruby，而不是msys/ruby，是因为我先用它试了一番，在Gem安装Jekyll时总是有个库编不过去。我没有深究下去，先这么着吧，以后有空再找找解决方法。

安装了这个版本的Ruby之后，需要将[shell type](https://github.com/msys2/msys2/wiki/MSYS2-introduction#subsystems)设为mingw64进入MSYS2环境，也就是从“MSYS2 MinGW 64-bit”而不是“MSYS2 MSYS”进。前者会把/mingw64/bin加入到PATH里，而此版本Ruby安装在/mingw64/bin中。

然后，我用Gem安装Bundler和Jekyll：

```
gem install bundler jekyll
```

我已经有个很久没写过的旧博客，需要升级一下，所以进入博客的目录，执行了一下：

```
bundle update
```

据说升级Gem是一件很折腾人的事情，Ruby程序员往往一次升级单个Gem，一次性bundle update，容易出问题。我也是反复在bundle install和bundle update之间折腾，才搞定了这件事。具体可以查一下[bundle install和bundle update之间的区别](https://bing.com/search?q=bundle%20install%20bundle%20update%20%E5%8C%BA%E5%88%AB)。

升级完成之后，就可以继续[用Jekyll写博客](https://jekyllrb.com/docs/step-by-step/01-setup)了。

PS：

我为了复习怎么用Jekyll，粗略的看了一下[Jekyll Step by Step Tutorial](https://jekyllrb.com/docs/step-by-step/01-setup)。它在第一步里讲使用jekyll serve来运行Jekyll，这和我们平常使用bundle exec jekyll serve的方式有点不一样。实际上在第十步里有解释[怎么一回事](https://jekyllrb.com/docs/step-by-step/10-deployment/#gemfile)。我也不是很熟悉Ruby，使用Jekyll的很多人估计也是这样。反正Bundler大概是一个处理相同Gem的不同版本被同时引用的工具，我们记得用它就行了。

另外，这一篇虽然标题是Windows10，并不是说不适用于之前的Windows版本，我只是没有试过。