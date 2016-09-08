---
layout: post
title: ubuntu locale问题初探(一) 
categories: Linux系统配置 
tags: ubuntu 
---

今天心血来潮，装了ubuntu server版本，选的是英文安装，之后发现中文会乱码，于是上网找了一下解决方案。说下结论，问题无法解决。但是，有一些有用的点还是可以记录下来，以备其他发行版参考。

首先，针对desktop版本，以下方法很可能有效：

Ubuntu 先查看一下 /usr/share/i18n/SUPPORTED 这个文件受支持的 locale 的设定，然后用命令激活即可。
运行激活命令:
locale-gen en_US.UTF-8
locale-gen fr_FR
locale-gen zh_CN.UTF-8
locale-gen zh_CN
locale-gen zh_CN.GBK
locale-gen zh_CN.GB18030
直接编辑/etc/locale.gen文件，基本达到同样效果

然后设定系统默认的编码语言:
vi /etc/default/locale
输入:
LANG="zh_CN.UTF-8"
LANGUAGE="zh_CN:zh"
退出保存，然后执行：

locale-gen --purge
重新生成，带上--purge(用来删除所有旧的配置，在出现问题时很有用),不然在编译时可以会报错，然后重启系统即可。

针对server版本，以下是问题说明：
可以有三种方法解决该问题，分别详细介绍如下。

第一种：安装zhcon软件包
$ sudo apt-get install zhcon
即可将zhcon软件包安装上，它其实就相当于一个Ubuntu的UC-DOS程序，是一个汉字外挂。既然是外挂就必然要占用一定的系统资源，根据实际需求可选用该方法。

第二种：使用putty、securteCRT等虚拟终端软件
直接修改虚拟终端界面配置项目中的字体编码为UTF-8即可。其实就是仍然采用了Ubuntu Server默认的zh_CN.UTF-8汉字编码，但在虚拟终端中经过“编码修正”后正确显示出来，因为Windows系统下是采用GBK作为系统默认 编码的，故在Windows下，无论是虚拟机，还是默认的虚拟终端界面，显示汉字都是乱码或菱形符号。该方法使用较广，但在实际终端下，仍然无法正常显示 汉字，因为此时系统的默认编码还是zh_CN.UTF-8，服务器上的实际终端还是不能认识zh_CN.UTF-8这种编码。故引出第三种方法。

第三种：修改Ubuntu的配置文件/etc/default/locale
将原来的配置内容修改为
LANG=”en_US.UTF-8″
LANGUAGE=”en_US:en”
再在终端下运行：
$ locale-gen -en_US:en
注销或重启后，Ubuntu Server真正服务器实体终端就恢复成了英文的语言环境。
所以，此方法不是真正意义上的中文化，而是恢复英文的默认编码
