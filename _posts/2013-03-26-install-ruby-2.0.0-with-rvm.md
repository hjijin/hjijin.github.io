---
layout: post
title: "安装最新版Ruby & Rails"
category: RoR
excerpt: 想体验最新版的ruby和rails吗？
tags: ruby
---

{% include JB/setup %}

2013年2月24日,发布了Ruby的最新版本*2.0.0*, 想尝鲜的童鞋可以参考参考：
我是在RVM里装的，同时我也建议用RVM,可以控制ruby版本切换。
安装RVM 

    $ curl -L https://get.rvm.io | bash -s

确定你的rvm版本是不是最新的，可以执行如下命令更新：

    $rvm get stable

检查一下安装的需求环境：

    $ rvm requirements


我的系统下面我还安装了这些东西：

    $ brew install libtool libxslt libksba openssl

再安装一个YAML库（不同操作系统的安装命了略有不同）：

   # Debian-based
    $ apt-get install libyaml-dev

    # Fedora/CentOS/RHEL Linux
    $ yum install libyaml-devel

    # Mac
    $ brew install libyaml

最后需要告诉RVM安装ruby2.0.0*时，OpenSSL的路径
  
    $ rvm install 2.0.0 --with-openssl-dir=$HOME/.rvm/usr

一会儿，ruby2.0.0*就装好了，你可以安装rails最新版：

    $ gem install rails --version 4.0.0.beta1 --no-ri --no-rdoc

来体验新版本的变化！

*Go！Go！Go！*  