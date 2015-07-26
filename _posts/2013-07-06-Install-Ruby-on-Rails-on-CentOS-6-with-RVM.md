---
layout: post
title: "CentOS 搭建 Ruby on Rails 部署环境"
category: Linux
excerpt: 在CentOS上搭建 Ruby on Rails 部署环境，记录下安装的过程，留给自己备忘。
tags: IT技术
---

最近呢想学习下在服务器上部署Rails项目，打算在在CentOS上搭建 Ruby on Rails 部署环境，现在记录下安装的过程，留给自己备忘。
我在[CentOS](http://www.centos.org/modules/tinycontent/index.php?id=30)官网下载了*CentOS-6.4* 的镜像文件，在 Mac 的 Vmware 	Fusion 里安装的。
过程...

第一步：更新centos源
==================

安装好系统后，先将系统的依赖包更新一下：

    sudo yum update

还要安装一些系统包：
    
    yum -y install gcc*
    yum install -y gcc-c++*
    yum install -y curses-devel*
    yum install -y curses*
    yum install -y cmake bison  bash
    yum install curl
    yum install curl-devel
    yum install zlib-devel
    yum install openssl-devel
    yum install perl
    yum install cpio
    yum install expat-devel
    yum install gettext-devel
    yum install autoconf
    yum install sqlite*
    yum install ruby-devel sqlite-devel
    yum install ntp*

第二步：安装rvm,rails,ruby
============================

####RVM

直接在终端输入：

    curl -L get.rvm.io | bash -s stable

将rvm路径加载到系统路径中，然后直接输入

    rvm -v

如果出现版本信息，说明rvm已经安装成功，安装完成以后Reload一下RVM：

    source ~/.rvm/scripts/rvm

这样以后，可能你换需要安装一些rvm需要的依赖包：

    rvm requirements

执行这个命令后，你只需要根据他的提示将缺的包装好就是了。通常你成功后再次执行这个命令他会返回:

    $ rvm requirements
    Installing requirements for centos, might require sudo password.
    Requirements installation successful.

####Ruby

    rvm pkg install readline
    rvm install 1.9.3 --with-readline-dir=$rvm_path/usr
    rvm 1.9.3 --default
    
上面安装ruby的过程我就不罗嗦了，如果出现类似下面的错误信息：

    $ rvm install 1.9.3
    Searching for binary rubies, this might take some time.
    Installing requirements for centos, might require sudo password.
    There is no checksum for 'https://dl.fedoraproject.org/pub/epel/RPM-GPG-KEY-EPEL-6' or 'RPM-GPG-KEY-EPEL-6', it's not possible to     validate it.
    This could be because your RVM install's list of versions is out of date. You may want to
    update your list of rubies by running 'rvm get stable' and try again.
    If that does not resolve the issue and you wish to continue with unverified download
    add '--verify-downloads 1' after the command.
    ...

运行：`$ rpm -ivh https://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm`


最后是安装rails 和 相关依赖包
=========================

运行

    gem install rails -v3.2.13

    ...


到此，环境基本搭建成功。