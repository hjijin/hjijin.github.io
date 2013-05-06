---
layout: post
title: "Concerns In Rails4"
category: RoR
excerpt: 搭建好ruby2.0和rails4.0，发现Controller和Model下都多了一个文件夹Concerns.
tags: rails
---

{% include JB/setup %}

Concerns 在rails3已经支持，但是在Rails4中，它变得更加”official“。Concerns 或 Service Objects 是否是最好的办法存在着一些争议。DHH支持Concerns，但OO支持者倾向于选择Service Objects。我相信他们都有自己的存在意义，并且可以一起使用。

##看个例子

>

选择一个ActiveRecord model 来看看这样提取的道理，刚开始，我为Concerns单独创建了validations, assocations, 和 accessors等验证，例如：

    # app/models/trader.rb
    class Trader
      include Accessors
      include Validations
      include Associations
      ...
    end

后来，我觉得这并不是很好的idea

    1.如果我添加新的Trader行为，那我可能会需要改变一个以上的Concerns。

    2.这样是不可取的，我要重用这些Concerns到别的model。

后来我试图根据‘topic’区分代码，于是有了以下：

    # app/models/trader.rb
    class Trader
      include BasicInfo
      include FriendlyURL
      include Location
      include OpeningHours
      include Schedule
      include Permissions
    end

一般情况下，我认为一个Concerns应该在域概念之后被命名，我想上述是对的，也许除了FriendlyURL。

再来看一个Schedule concern的例子：

    # app/models/concerns/trader/schedule.rb
    class Trader
      module Schedule
        extend ActiveSupport::Concern
    
        included do
          attr_accessible :market_days_ids
    
          has_many :appearances
          has_many :market_days, :through => :appearances
        end
    
        def open?
          ...
        end
    
        def closed?
          ...
        end
      end
    end

像上面那样，它可以包括混合的validations, accessibilty, associations 以及 plain 方法，这是一个更好的方法来区分相关的功能。

##共享Concerns

>

文档和看过的其他文章让我很模糊：Concerns之间如以共享多个model。比方说，有两个名称相同的Concerns：

    * app/models/concerns/schedule.rb
    * app/models/concerns/trader/schedule.rb

而model是这样：

    # app/models/trader.rb
    class Trader
      include Schedule
    end

这种情况下 Concerns在`app/models/concerns/trader/schedule.rb`的就被调用。

但是，如果Concerns被删除，model就会获取其他的Concerns了。也就是说，model会首先查找它自己的Concerns的直接路径	，没有找到就会向它的顶层去找；
要证明共享Concerns，有一个更形象的方法：

    # app/models/trader.rb
    class Trader
      include Shared::Schedule
    end
    
    # app/models/concerns/shared/schedule.rb
    module Shared::Schedule
      extend ActiveSupport::Concern
      ...
    end

希望这对你也有帮助。

*注*: 原文出自[andywaite](http://blog.andywaite.com/2012/12/23/exploring-concerns-for-rails-4/)