---
layout: post
title: "First Use RQRcode"
category: RoR
excerpt: 因为业务需求，需要使用二维码，刚好这也是第一次弄，也就记下来吧。
tags: rails
---

{% include JB/setup %}

###准备工作
> 安装  gem install rqrcode-rails3
> 
> 安装  gem install mini_magick

###打开Gemfile 文件 加入以下两句话
> gem 'rqrcode-rails3'
> 
> gem 'mini_magick'

####在controller 下加入以下代码：
    url = "www.baidu.com"
    @qr = RQRCode::QRCode.new(url, :size => 6, :level => 'q'.to_sym)

####在view里加入以下代码：

    <style type="text/css">
      table {
        border-width: 0;
        border-style: none;
        border-color: #0000ff;
        border-collapse: collapse;
      }
      td {
        border-width: 0;
        border-style: none;
        border-color: #0000ff;
        border-collapse: collapse;
        padding: 0;
        margin: 0;
        width: 10px;
        height: 10px;
      }
      td.black { background-color: #000; }
      td.white { background-color: #fff; }
    </style>
    <table>
      <% @qr.modules.each_index do |x| %>
        <tr> 
          <% @qr.modules.each_index do |y| %>
            <% if @qr.dark?(x,y) %>
              <td class="black"/>
            <% else %>
              <td class="white"/>
            <% end %>
          <% end %>
        </tr>
      <% end %>
    </table>

最终效果：[来源](http://whomwah.github.io/rqrcode/)