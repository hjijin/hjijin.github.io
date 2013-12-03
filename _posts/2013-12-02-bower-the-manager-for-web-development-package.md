---
layout: post
title: "Bower--用于web前端开发的包管理器"
category: web
excerpt: 因为业务需求，需要使用二维码，刚好这也是第一次弄，也就记下来吧。
tags: 前端
---

{% include JB/setup %}

Bower是用于web前端开发的包管理器。对于前端包管理方面的问题，它提供了一套通用、客观的解决方案。它通过一个API暴露包之间的依赖模型，这样更利于使用更合适的构建工具。Bower没有系统级的依赖，在不同app之间也不互相依赖，依赖树是扁平的。

Bower运行在Git之上，它将所有包都视作一个黑盒子。任何类型的资源文件都可以打包为一个模块，并且可以使用任何规范（例如：AMD、CommonJS等）。

###安装Bower
Bower依赖[Node](http://nodejs.org/) 和 [npm](http://npmjs.org/)。通过npm 安装到全局环境中：

		npm install -g bower
###使用
>安装之后，可以通过`bower help`命令可以获取更多帮助信息。了解了这些 信息就可以开始了。

####安装包及其依赖的包

	# Using the dependencies listed in the current directory's bower.json
	  bower install
	# Using a local or remote package
	  bower install <package>
	# Using a specific Git-tagged version from a remote package
	  bower install <package>#<version>
	
	# 例子  
	➜  ~  bower install jquery
	      bower jquery#*  cached git://github.com/components/    jquery.git#2.0.3
		  	bower jquery#*  validate 2.0.3 against git://github.com/components/jquery.git#*
		  
>jQuery 会被下载到 ./bower_components 目录下，在页面上直接调用就可以了, 不建议直接修改此目录中的内容。
查看目录结构
    $ tree bower_components/
Bower 在下载的时候会去 server 上找名字对应的 git 库，下载后切换到对应的版本，如果未指定则是最新的。

除了按名字下载，还有其他方式

	直接下载 git 库
	bower install git://github.com/components/jquery.git
	github 的别名，自动解析成 git 库
	bower install components/jquery (same as above)
	下载线上的任意文件
	bower install http://foo.com/jquery.awesome-plugin.js
	下载本地库
	bower install ./repos/jquery
如果只是下载的话是不需要配置文件的。
####查找包
	bower search [<name>]
>只需执行bower search命令即可列出所有已经注册的包。

####使用包
  做简单的使用方式就是通过script 标签直接引入已经安装的包：

	<script src="/bower_components/jquery/index.js"></script>
对于更复杂的项目，你可能更希望合并所有脚本或者使用模块加载器（module loader）。 Bower只是一个包管理器，不过你可以使用很多其他工具 -- 例如 [Sprockets](https://github.com/sstephenson/sprockets) 和 [RequireJS](http://requirejs.org/) -- 这些都能帮你完成你所希望的工作。

####注册包
可以注册自己的包，这样其他人也可以使用了

	bower register project git://github.com/yourname/project
这个操作只是在服务器上保存了一个隐射，服务器本身不托管代码。现在还有没有直接移除已注册包的途径。如果需要，可以[request a package be unregistered](https://github.com/bower/bower/issues/120)。

####卸载包

卸载已经安装到本地的包：

	bower uninstall <package-name>
	
####配置文件
每个包应该有一个配置文件，描述包的信息，bower 的配置文件为 component.json。

	{
  		"name": "myProject",
  		"version": "1.0.0",
  		"main": "./path/to/main.css",
  		"dependencies": {
    		"jquery": "~1.7.2"
  		}
	}
name 和 version 描述包的名称和版本，dependencies 描述这个包依赖的其他包。main 指定包中的静态文件，可以为一个数组。

除了包的配置文件，bower 还有一个全局的配置文件(~/.bowerrc)。

	{
 		 "directory" : "components",
  		"json"      : "component.json",
  		"endpoint"  : "https://bower.herokuapp.com",
  		"searchpath" : ["https://bower.herokuapp.com"]
	}
directory 为 install 时生成的目录，json 指定配置文件的名称。

endpoint 指定 bower server，用于储存包的信息，默认是 bower 官方的，也可以自己搭建 bower server。可以通过 register 命令将包信息注册到 endpoint 上。

searchpath 可以指定一系列的 bower server，但是只是用于查询。首先查询 endpoint，如果没有再按顺序查询 searchpath。

###项目中使用
在项目中使用 bower 也比较简单。

给自己的项目也添加一个 component.json 配置文件，执行 `bower install`，bower 会将依赖的包下载到 bower_components 的目录下。

如果依赖的包还依赖其他包，bower 也会下载到本地，直到把所有依赖的包都下载完成。bower 的目录结构是扁平化的，所有的包都在同级目录下。

如果想查看有哪些包和文件，可执行 `bower list --path`。比如 install 了 jquery，可以看到以下信息

	{
  		"jquery": "components/jquery/jquery.js"
	}
现在就可以使用了，在当前目录建一个页面，script 嵌入需要的 js，如果想对 js 做模块化管理可使用 [seajs](http://seajs.org/docs/)。

###总结
Bower 非常简单易用，只要创建一个 component.json 添加依赖包的配置就可以使用了，再不需要到处去找了。

现在就开始使用吧，[找找有没有你想用的组件](http://sindresorhus.com/bower-components/)。