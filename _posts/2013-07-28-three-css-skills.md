---
layout: post
title: "三个 CSS 技巧"
category: CSS
excerpt: 各种浏览器之间的竞争的白热化意味着越来越多的人现在开始使用那些支持最新、最先进的W3C Web标准的设备，以一种更具交互性的方式来访问互联网。
tags: 前端
---

{% include JB/setup %}

  过去的半个多月，一直在学习CSS，发现了几个比较好的技巧，于是将它记了下来。

###在CSS中用attr()显示HTML属性值

attr()功能早在CSS 2.1标准中就已经出现，但现在才开始普遍流行。它提供了一个巧妙的方法在CSS中使用HTML标签上的属性，在很多情况下都能帮你省去了以往需要JAVAscript处理的过程。

要想使用这个功能，你需要用到三种元素：一个:before 或 :after CSS伪类样式， .content属性，和一个带有你想使用的HTML属性名称的attr()表达式。例如，想去显示<h3>标题上的data-prefix属性的值，你可以写成这样：

    h3:before {
      content: attr(data-prefix) " ";
    }
 
    <h3 data-prefix="Custom prefix">This is a heading</h3>

显然，这个例子并没有展示它有多大用处，只是展示了它的基本用法。让我们来试一个更有用的例子，attr()的一个极好的应用就是当用户打印页面时将页面链接显示出来。为了实现这个，你可以这样写：

    @media print {
      a:after {
        content: " (link to " attr(href) ") ";
      }
    }
 
    <a href="http://example.com">Visit our home page</a>

一旦你知道了这个技巧，你就会吃惊于很多时候它能给你的工作带来的方便！

提示：在新版的CSS3标准中，attr()功能被扩展，可以用在各种CSS标记中。

###使用counter()在列表中自动添加序号

另外一个在CSS 2.1在就已经支持的功能是counter()，使用它，你能方便的在页面标题，区块和其它各种连续出现的页面内容上添加序号。有了它，你就不必限制于只能使用<ol>来实现这个效果，你可以更灵活的在页面上使用自定义数字序列。

关键就是它真的很简单：在:before伪类里的content属性加入counter()：

    body {
      counter-reset: heading;
    }
 
    h4:before {
      counter-increment: heading;
      content: "Heading #" counter(heading) "."; 
    }

如果你想知道更多关于这个counter归零和自增方法的知识，请参考关于这个主题的Mozilla Developer Network页面。里面有个极好的如何使用嵌套counter的例子。

###使用calc()做算术

最后，但不是最不重要的，让我们来说说calc()功能。这个函数能让你执行简单的算术计算，例如计算元素的长宽，免去了你写不易维护的Javascript代码。这个函数支持所有简单的基本算术运算，包括加减乘除。

比方说，你想创建一个元素，使它的宽度占满它的父元素，但还要留出一部分像素宽做其它用处：

    .parent {
      width: 100%;
      border: solid black 1px;
      position: relative;
    }
 
    .child {
      position: absolute;
      left: 100px;
      width: calc(90% - 100px);
      background-color: #ff8;
      text-align: center;
    }

漂亮吧，不是吗？

我发现，CSS已经成熟到在某些方法可以替代javascript，极大的简化了WEB开发人员的工作。