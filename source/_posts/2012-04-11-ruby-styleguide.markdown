---
layout: post
title: "Ruby Styleguide"
date: 2012-04-11 11:34
comments: true
categories: [Ruby]
---

#### 注： 本文为[Github Ruby Styleguide](https://github.com/styleguide/ruby)的中文翻译， 边翻译边过了一遍，发现这些风格约定很有其道理。推荐！

#### 代码风格 (Coding Style)

----

* 使用两个空格来保持缩进
* 每行应该小于80个字符（*Texmate和sublime text2 都可以设置*）
* 末尾不要留空格
* 在操作符前后， 逗号后面， 冒号和分号前后留空格。
  
  {% codeblock lang:ruby %}
    sum = 1 + 2
    a, b = 1, 2
    1 > 2 ? true : false; puts 'Hi'
    [1, 2, 3].each { |e| puts e }
  {% endcodeblock %}
  
* 在(和[后面, 或者]和)前面不能有空格。

  {% codeblock lang:ruby %}
    some(arg).other
    [1, 2, 3].length
  {% endcodeblock %}
  
* case和when应该保持同样的缩进深度.

  {% codeblock lang:ruby %}
    case
    when song.name == 'Misty'
      puts 'Not again!'
    when song.duration > 120
      puts 'Too long!'
    when Time.now.hour > 21
      puts "It's too late"
    else
      song.play
    end
    # another style
    kind = case year
           when 1850..1889 then 'Blues'
           when 1890..1909 then 'Ragtime'
           when 1910..1929 then 'New Orleans Jazz'
           when 1930..1939 then 'Swing'
           when 1940..1950 then 'Bebop'
           else 'Jazz'
           end
  {% endcodeblock %}

<!--more-->

* 使用空行来隔离区分一个方法内部的逻辑。

  {% codeblock lang:ruby %}
    def some_method
      data = initialize(options)

      data.manipulate!

      data.result
    end

    def some_method
     result
    end  
  {% endcodeblock %}
  
  
#### 文档 (Documentation)

----

** 最好使用[TomDoc](http://tomdoc.org/)来规范你的文档格式 **

{% codeblock lang:ruby %}
  # Public: Duplicate some text an arbitrary number of times.
  #
  # text  - The String to be duplicated.
  # count - The Integer number of times to duplicate the text.
  #
  # Examples 
  #
  #   multiplex('Tom', 4)
  #   # => 'TomTomTomTom'
  #
  # Returns the duplicated String.
  def multiplex(text, count)
    text * count
  end
{% endcodeblock %}


#### 语法 (Syntax)

----

* 当在def方法不接受任何参数的时候省略括号。 当有参数的时候， 使用括号。 

{% codeblock lang:ruby %}
  def some_method
    # body omitted
  end

  def some_method_with_arguments(arg1, arg2)
    # body omitted
  end
{% endcodeblock %}

* 不要使用For.

{% codeblock lang:ruby %}
  arr = [1, 2, 3]

  # bad
  for elem in arr do
    puts elem
  end

  # good
  arr.each { |elem| puts elem }
{% endcodeblock %}

* 在if/unless语句中不要用then.

{% codeblock lang:ruby %}
  # bad
  if some_condition then
    # body omitted
  end

  # good
  if some_condition
    # body omitted
  end
{% endcodeblock %}

* 在条件极其琐碎比较多的情况下应该避免三元操作符(?:)， 但是单行条件的情况下是可以用三元操作符的。

{% codeblock lang:ruby %}
  # bad
  result = if some_condition then something else something_else end

  # good
  result = some_condition ? something : something_else
{% endcodeblock %}

* 不要嵌套使用三元操作符(?:), 要用if/unless语句分开。

{% codeblock lang:ruby %}
  # bad
  some_condition ? (nested_condition ? nested_something : nested_something_else) : something_else

  # good
  if some_condition
    nested_condition ? nested_something : nested_something_else
  else
    something_else
  end
{% endcodeblock %}

* 禁止使用and和or, 要使用 &&和||
* 避免多行的三元操作符(?:)， 用if/unless语句代替
* 单行语句的情况下，使用如下形式:

{% codeblock lang:ruby %}
  # bad
  if some_condition
    do_something
  end

  # good
  do_something if some_condition
{% endcodeblock %}

* 禁止在unless语句中使用else。

{% codeblock lang:ruby %}
  # bad
  unless success?
    puts 'failure'
  else
    puts 'success'
  end

  # good
  if success?
    puts 'success'
  else
    puts 'failure'
  end
{% endcodeblock %}

* if/unless/while语句后面的条件不要用括号包上， 除非这个条件里面有赋值运算。

{% codeblock lang:ruby %}
  # bad
  if (x > 10)
    # body omitted
  end

  # good
  if x > 10
    # body omitted
  end

  # ok
  if (x = self.next_value)
    # body omitted
  end
{% endcodeblock %}

* 单行的blocks要用{...}代替do...end， 在多行blocks下面应该用do...end. 在流程控制和方法定义方面应总是使用 do...end ， 有方法链式调用情况下应该使用{...}

{% codeblock lang:ruby %}
  names = ["Bozhidar", "Steve", "Sarah"]

  # good
  names.each { |name| puts name }

  # bad
  names.each do |name|
    puts name
  end

  # good
  names.select { |name| name.start_with?("S") }.map { |name| name.upcase }

  # bad
  names.select do |name|
    name.start_with?("S")
  end.map { |name| name.upcase }
{% endcodeblock %}

* 在不需要的地方避免使用return。

{% codeblock lang:ruby %}
  # bad
  def some_method(some_arr)
    return some_arr.size
  end

  # good
  def some_method(some_arr)
    some_arr.size
  end
{% endcodeblock %}

* 当方法的参数中设置默认值的时候， 在=操作符前后使用空格。

{% codeblock lang:ruby %}
  # bad
  def some_method(arg1=:default, arg2=nil, arg3=[])
    # do something...
  end

  # good
  def some_method(arg1 = :default, arg2 = nil, arg3 = [])
    # do something...
  end
{% endcodeblock %}

* 条件中使用了赋值操作符应该用括号包围。

{% codeblock lang:ruby %}
  # good - shows intented use of assignment
  if (v = array.grep(/foo/)) ...

  # bad
  if v = array.grep(/foo/) ...

  # also good - shows intended use of assignment and has correct precedence.
  if (v = self.next_value) == "hello" ...
{% endcodeblock %}

* 用 ||= 来初始化变量.

{% codeblock lang:ruby %}
  # set name to Bozhidar, only if it's nil or false
  name ||= 'Bozhidar'
{% endcodeblock %}

* 不要用 ||= 去初始化boolean型变量。

{% codeblock lang:ruby %}
  # bad - would set enabled to true even if it was false
  enabled ||= true

  # good
  enabled = true if enabled.nil?
{% endcodeblock %}

* 不要使用Perl式的特殊变量（像$0-9, $, 等）。 应该使用比较长的，就像 $PROGRAM_NAME.
* 在方法名和括号之间不要有空格。

{% codeblock lang:ruby %}
  # bad
  f (3 + 2) + 1

  # good
  f(3 + 2) + 1
{% endcodeblock %}

* 如果方法的第一个参数是括号， 那么这个方法总是应该使用括号。就像 f((3 + 2) + 1) 这样。

* 使用 _ 符号来表示不使用的block参数。

{% codeblock lang:ruby %}
  # bad
  result = hash.map { |k, v| v + 1 }

  # good
  result = hash.map { |_, v| v + 1 }
{% endcodeblock %}

#### 命名 (Naming)

----

* 为方法和变量使用蛇形 snake_case
* 为类和模块使用骆驼形 CamelCase (HTTP,RFC,XML 应该大写)
* 自定义的常量使用全大写蛇形 SCREAMING_SNAKE_CASE
* 断言类方法（返回boolean值）的命名应该以问号结尾。就像 Array#empty?
* 有潜在‘危险’的方法（修改self或者是参数，类似于 exit! 这样的）， 应该以叹号结尾!. 这类方法叫Bang method.

#### 类 (Classes)

----

* 避免使用类变量 @@variables，因为他们在继承中表现的比较猥亵。

{% codeblock lang:ruby %}
  class Parent
    @@class_var = 'parent'

    def self.print_class_var
      puts @@class_var
    end
  end

  class Child < Parent
    @@class_var = 'child'
  end

  Parent.print_class_var # => will print "child"
{% endcodeblock %}

一个父类的类变量在其继承体系的所有类里面都是共用的，类实例变量@class_vers, 才是per-class的， 当我们使用的时候， 优先选用类实例变量。

* 使用 def self.method 来定义单例方法(singleton methods)， 这样会使重构的时候更加方便。

{% codeblock lang:ruby %}
  class TestClass
    # bad
    def TestClass.some_method
      # body omitted
    end

    # good
    def self.some_other_method
      # body omitted
    end
{% endcodeblock %}

* 避免使用 class << self ， 除非是必须的， 比如单独的accessors和aliased属性。

{% codeblock lang:ruby %}
  class TestClass
    # bad
    class << self
      def first_method
        # body omitted
      end

      def second_method_etc
        # body omitted
      end
    end

    # good
    class << self
      attr_accessor :per_page
      alias_method :nwo, :find_by_name_with_owner
    end

    def self.first_method
      # body omitted
    end

    def self.second_method_etc
      # body omitted
    end
  end
{% endcodeblock %}

* public, protected, 和 private 应该和方法定义缩进一样， 并且在其上方留一空行。

{% codeblock lang:ruby %}
  class SomeClass
    def public_method
      # ...
    end

    private
    def private_method
      # ...
    end
  end
{% endcodeblock %}


#### 异常 (Exceptions)

----

* 不要用异常来控制流程

{% codeblock lang:ruby %}
  # bad
  begin
    n / d
  rescue ZeroDivisionError
    puts "Cannot divide by 0!"
  end

  # good
  if d.zero?
    puts "Cannot divide by 0!"
  else
    n / d
  end
{% endcodeblock %}

* 避免rescuing Exception这个类。

{% codeblock lang:ruby %}
  # bad
  begin
    # an exception occurs here
  rescue
    # exception handling
  end

  # still bad
  begin
    # an exception occurs here
  rescue Exception
    # exception handling
  end
{% endcodeblock %}

关于异常处理，推荐 Exceptional Ruby这本书。

#### 集合 (Collections)

----

* 当你需要字符串数组的时候，最好使用%w

{% codeblock lang:ruby %}
  # bad
  STATES = ['draft', 'open', 'closed']

  # good
  STATES = %w(draft open closed)
{% endcodeblock %}

* 当需要处理唯一元素的时候，使用Set来代替Array, Set是无重复值的无序集合。它既包含Array的运算功能,同时又兼有Hash的高速搜索功能。
* 使用符号类型来代替字符串型来作为Hash的keys。

{% codeblock lang:ruby %}
  # bad
  hash = { 'one' => 1, 'two' => 2, 'three' => 3 }

  # good
  hash = { one: 1, two: 2, three: 3 }
{% endcodeblock %}


#### 字符串 (Strings)

----

* 使用#{}来代替字符串+连接

{% codeblock lang:ruby %}
  # bad
  email_with_name = user.name + ' <' + user.email + '>'

  # good
  email_with_name = "#{user.name} <#{user.email}>"
{% endcodeblock %}

* 如果不需要转义什么特殊字符什么的， 应该用单引号

{% codeblock lang:ruby %}
  # bad
  name = "Bozhidar"

  # good
  name = 'Bozhidar'
{% endcodeblock %}

* 当需要构建大的数据块的时候应该避免使用String#+方法， 应该使用 String#<< , 这个方法比String#+快。

{% codeblock lang:ruby %}
  # good and also fast
  html = ''
  html << '<h1>Page title</h1>'

  paragraphs.each do |paragraph|
    html << "<p>#{paragraph}</p>"
  end
{% endcodeblock %}

#### 正则表达式 (Regular Expressions)

----

* 使用named groups （只支持Ruby1.9+）

{% codeblock lang:ruby %}
  # bad
  /(regexp)/ =~ string
  ...
  process $1

  # good
  /(?<meaningful_var>regexp)/ =~ string
  ...
  process meaningful_var
{% endcodeblock %}

(下面说明和代码是我自己加的内容：)
你可以定义每一部分的正则，然后命名成组，并且在后面加上 
{0}，表明不获取匹配内容，然后在使用\g引用对用的组，组成 
大的正则，并且加上/x修饰。这样比较复杂的正则，会有更好的可读性。。另一部分示例代码：

{% codeblock lang:ruby %}
users = %w{  
   alice:10.23.52.112:true  
   bob:192.168.10.34:false  
 }  
   
 user_regexp = %r{  
   (?<username> [a-z]+ ){0}  
   (?<ip_number> [0-9]{1,3} ){0}  
   (?<ip_address> (\g<ip_number>\.){3}\g<ip_number> ){0}  
   (?<admin> true | false ){0}  
   \g<username>:\g<ip_address>:\g<admin>  
 }x  
  
users.each do |u|  
    r = user_regexp.match(u)  
    puts "User #{r[:username]} is from #{r[:ip_address]}"  
end
{% endcodeblock %}

* 小心使用 ^ 和 $ , 它们是来匹配行的开始和结束，不是字符串。 如果你想匹配整个string，则使用 \A和\Z

{% codeblock lang:ruby %}
  string = "some injection\nusername"
  string[/^username$/]   # matches
  string[/\Ausername\Z/] # don't match
{% endcodeblock %}

* 使用 x 来修饰复杂的正则， 这样更可读，还可加注释，只是需要小心空格

{% codeblock lang:ruby %}
  regexp = %r{
    start         # some text
    \s            # white space char
    (group)       # first group
    (?:alt1|alt2) # some alternation
    end
  }x
{% endcodeblock %}


#### 百分号字面量 (Percent Literals)

----

* 使用%w毫无压力
{% codeblock lang:ruby %}
  STATES = %w(draft open closed)
{% endcodeblock %}

* 使用%()来引用单行字符串，相当于双引号（相当于%Q()），对于多行的字符串，要使用here文档。

{% codeblock lang:ruby %}
  # bad (no interpolation needed)
  %(<div class="text">Some text</div>)
  # should be '<div class="text">Some text</div>'

  # bad (no double-quotes)
  %(This is #{quality} style)
  # should be "This is #{quality} style"

  # bad (multiple lines)
  %(<div>\n<span class="big">#{exclamation}</span>\n</div>)
  # should be a heredoc.

  # good (requires interpolation, has quotes, single line)
  %(<tr><td class="name">#{name}</td>)
{% endcodeblock %}

* 超过一个/符合的正则应该使用 %r

{% codeblock lang:ruby %}
  # bad
  %r(\s+)

  # still bad
  %r(^/(.*)$)
  # should be /^\/(.*)$/

  # good
  %r(^/blog/2011/(.*)$)
{% endcodeblock %}

####Above all else

----

Follow your ![](https://a248.e.akamai.net/assets.github.com/images/icons/emoji/heart.png?v5) 





