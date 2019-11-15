---
layout:     post
title:      Linux源码编译TMUX安装
subtitle:   踩坑记录
date:       2019-11-15
author:     HY
header-img: img/Myimg/6.jpg
catalog: true
tags:
    - Linux



---

> 整篇文章Copy from [王环宇大佬](http://www.lamda.nju.edu.cn/wanghy/).
>
> ![宇哥NIUBI](../img/Myimg/emoji_1.jpg)



# TMUX安装和Linux源码编译



经过了将近两个小时的努力，终于在Pascal2上配置好了tmux

在安装过程中有很多的问题，主要是由于不是root用户，所以无法直接安装在root环境下，只能在自己的home环境下搭建一个软件安装的地方（我专门用于安装本地软件的位置是`\home\yuhao\software`）在这个位置下有类似的`bin` `include`等

安装[tmux](https://github.com/tmux/tmux)，有两个依赖，在官网中找到下载地址，下载在服务器中。由于没有root权限，所以需要安装在本地，安装在本地的一个重要操作是：(会自动在目录中生成相应的bin，include等)

```python
libncurses5-dev
https://github.com/downloads/libevent/libevent/libevent-2.0.21-stable.tar.gz
ncurses5.7
https://invisible-mirror.net/archives/ncurses/ncurses-5.7.tar.gz
```

config设定（最重要的一步）

```bash
./configure --prefix=/home/wanghy/software
make && make install
```

安装好了依赖之后，然后就是安装tmux，下载源码到本地，同样地进入文件夹

```bash
./configure --prefix=/home/wanghy/software CFLAGS="-I$HOME/include" LDFLAGS="-L$HOME/lib"
make && make install
# $HOME 在这里即为我的~/software
# 由于有的依赖文件在home目录下，所以需要进行后面的一些设定
./configure --prefix=/home/wanghy/software CFLAGS="-I~/software/include" LDFLAGS="-L~/software/lib"
make && make install
```

这些都安装好了之后，出现了一些BUG：

1. 找不到某个.h文件

    找到这个文件，然后发现其在include下的一个子文件夹中，将其移入上一层文件夹

2. error while loading shared libraries: libevent-2.1.so.6:cannot open shared object file

    在全局的动态链接库中没有这个文件，这个文件在本地的lib中，所以在bashrc文件中加入一行

    `export LD_LIBRARY_PATH="/home/wanghy/software/lib/:$LD_LIBRARY_PATH"`

当一切都安装好了，本地的tmux其实已经可以用了，但如果需要其能够方便地直接使用，需要在bashrc文件中添加路径：`export PATH="/home/wanghy/software/bin/:$PATH"`

至此，一切安装就绪，尽情使用tmux！

需要的动态链接文件部分在software里面。