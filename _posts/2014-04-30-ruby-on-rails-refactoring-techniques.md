---
layout: post
title: "Ruby on Rails 简易重构例子"
category: ror
excerpt: 面对项目中臃肿的代码，突然想尝试下重构。
tags: rails
---

{% include JB/setup %}

重构的目的是提高代码的质量，清晰度和可维护性。 从别人的项目中接手过来的陌生的代码库，看里面的代码，总有种眼花缭乱的感觉，或许是因为他不符合自己的风格，又或许它真的很烂，不管怎么样，看过后，自己的想法是，能不能用更好的方法将他实现？苦于无重构的经验，于是乎开始了重构的学习。

##开始重构

###1.方法重命名

>   一个最简单最有效的重构是：重命名一个属性/属性，方法或对象。
>   一个有意义的命名，可以避免很多代码注释，使代码更清晰更易理解。重命名是重构里面最基本的重构技术
>   ，命名依赖于项目描述，以确保开发人员知道一看它究竟是什么。

>

###2.解释变量

>   如果有一个复杂的表达式（比如，if语句通常有一个罗里吧嗦的条件组），应该将条件赋值到一个临时变量，并给它一个描述性的标识符。
>   

例如：

    unless "This is a String with some CAPS".scan(/([A-Z])/).empty?
        puts "capitalised text was found"
    end


重构后：

    caps_not_found = "This is a String with some CAPS".scan(/([A-Z])/).empty?

    unless caps_not_found
      puts "capitalised text was found"
    end

###3.一个方法在另一个方法中调用

>   很多时候，一个公用的方法，往往很多其他的方法需要调用，然后每个方法都将这个公共方法赋值，更多的时候，赋值的变量名像那王大娘的裹脚布。

例如：

    def add_stuff
        1 + 1
    end

    def do_something
        temp_variable_with_descriptive_name = add_stuff
        puts "Number is #{temp_variable_with_descriptive_name}"
    end

重构后：

    def add_stuff
        1 + 1
    end

    def do_something
        puts "Number is #{add_stuff}"
    end 

###4.变量分开赋值
>   项目中经常出现一个变量赋值不止一次，并且每次赋值后，变量的意义都不一，这有悖SRP（单一职责原则）

例如：

    temp = 2 * (height + width)
    temp = height * width

重构后：

    perimeter = 2 * (height + width)
    area = height * width


###5.链接方法代替调用
>   
如果你有一个临时变量保存调用对象的方法的结果，并临时进行多次的方法调用，那么你应该考虑链接的方法调用来代替。 

例如：

    class College
        def create_course
            puts "create course"
        end
    
        def add_student
            puts "add student"
        end
    end
    
    temp = College.new
    temp.create_course
    temp.add_student
    temp.add_student
    temp.add_student

重构后：

    class College
        # static method so can be accessed without creating an instance
        def self.create_course
            college = College.new
            puts "create course"
            college # return new object instance
        end
    
        def add_student
            puts "add student"
            self # refers to the new object instance
        end
    end
    
    college = College.create_course
                .add_student
                .add_student
                .add_student


###6.提取方法

>   实现是非常简单的。就是将过长的方法它，过于复杂的模块提取到具有描述性标识的新方法。

例如：

    class Foo
        attr_reader :bar
    
        def initialize bar
            @bar = bar
          end
    
        def do_something
            puts "my baz" # notice this is duplication
            puts bar
        end
    
      def do_something_else
        puts "my baz" # notice this is duplication
        puts "Something else"
        puts bar
      end
    end

重构后：

    class Foo
      attr_reader :bar
    
      def initialize bar
        @bar = bar
      end
    
      def do_something
        baz
        puts bar
      end
    
      def do_something_else
        baz
        puts "Something else"
        puts bar
      end
    
      def baz
        puts "my baz"
      end
    end


###7.方法对象替代方法

>   您可能会遇到，你必须要使用提取方法，但临时局部变量的数目实在是太大了。要解决此问题，要做的第一件事是创建一个长的方法来命名新类并添加临时本地变量作为类/对象的属性/属性。

例如：

    class Foo
      def bar
        puts "We're doing some bar stuff"
      end
    
      def baz(a, b, c)
        if a == 'something'
          # do something
        end
    
        if b == 'else'
          # do else
        end
    
        if c == 'none'
          # do none 
        end
      end
    end   

重构后：

    class Foo
      def bar
        puts "We're doing some bar stuff"
      end
    end
    
    class Baz
      attr_accessor :a, :b, :c
    
      def initialize(a, b, c)
        @a = a
        @b = b
        @c = c
    
        if a == 'something'
          # do something
        end
    
        if b == 'else'
          # do else
        end
    
        if c == 'none'
          # do none
        end
      end
    end


###8.Collection Closure 方法替换 Loop

>   这个是我以前经常用的方式，要从一堆数据中提取出需要的内容放到一个新数组，我的惯用法是先声明一个空数组，然后对数据迭代。总感觉这样非常丑陋。

例如：

    managers = []
    employees.each do |e|
        managers << e if e.manager?
    end

重构后：
    
    managers = employees.select { |e| e.manager? }


###9.提取公共方法

>   当在两个单独的类中有重复的代码，那么最好提取重复的代码到一个超类，rails不是有个思想叫DRY（不要重复自己），并将它用于在多个地方。

例如：

    class Person
      attr_reader :first_name, :last_name
    
      def initialize first_name, last_name
        @first_name = first_name
        @last_name = last_name
      end
    
    end
    
    class MalePerson < Person
      # This is duplicated in the `FemalePerson` class
      def full_name
        first_name + " " + last_name
      end
    
      def gender
        "M"
      end
    end
    
    class FemalePerson < Person
      # This is duplicated in the `MalePerson` class
      def full_name
        first_name + " " + last_name
      end
    
      def gender
        "F"
      end
    end

重构后：

    class Person
      attr_reader :first_name, :last_name
    
      def initialize first_name, last_name
        @first_name = first_name
        @last_name = last_name
      end
    
      def full_name
        first_name + " " + last_name
      end
    end
    
    class MalePerson < Person
      def gender
        "M"
      end
    end
    
    class FemalePerson < Person
      def gender
        "F"
      end
    end


原文：[Refactoring Techniques](http://www.integralist.co.uk/posts/refactoring-techniques/#inline-temp)