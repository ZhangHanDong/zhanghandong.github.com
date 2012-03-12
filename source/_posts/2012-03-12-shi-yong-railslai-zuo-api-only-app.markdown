---
layout: post
title: "Rails Edge - 使用Rails来做API-Only App"
date: 2012-03-12 16:03
comments: true
categories: [Ruby]
---

#### 原文地址:  [点这里](https://github.com/rails/rails/blob/efd557a60cd976ac17be9e238111a551599caeb5/railties/guides/source/api_app.textile) 或者 [这里Edge Guides](http://edgeguides.rubyonrails.org/api_app.html)

** 本文并非原文翻译 **

***

## 这个导读你会学到：

* 什么是仅提供API的Rails应用

* 开始如何配置没有任何浏览器特性的Rails

* 如何选择你想包含的middlewares

* 如何选择你的controller里使用哪个modules

** 注意： 这个导读引入的特性并没有被全部实现。 先当文档来看 **

## 什么是一个API app？

传统意义上来说， 当人们说他用了Rails实现了一个“API”， 意味着他们的web应用提供一个程序可访问的API接口。就像Github提供的[这个API](http://developer.github.com/)， 你可以在你自己定制的客户端来使用它。

随着移动互联网时代的到来，以及各种客户端框架的出现， 越来越多的开发者使用Rails来构建后端， 供Web应用和本地应用(Native App)一起使用。例如， Twitter就在Web Application中和其他本地应用客户端（Native App）一样，都消耗着由自己的[公共API](https://dev.twitter.com/)提供的JSON资源,  而不是通过Rails生成动态的HTML来渲染用于和Server端通信的表单和链接。大多少开发者都把Web应用当作由HTML， CSS， Javascript构成的另一种客户端来处理，只是通过JSON来通信。

<!--more-->

## 为什么使用Rails来提供JSON APIs？

很多人在使用Rails构建一个JSON API时候思考的第一个问题就是：使用Rails输出JSON是不是有种杀鸡用牛刀的感觉？ 是不是应该用Siantra这类轻量级的框架就好了？

对于简单的API应用， 这些考虑可能适用， 但是复杂的， Rails更适合。

大多数人使用Rails的原因是它提供了默认的套件帮助我们快速的起步不用在烦杂的琐碎的东西上浪费时间。让我们看看适合API应用使用的东西：

#### 中间件层

*   Reloading:   R
ails应用支持transparent reloading. 不管是你的应用因为新增了文件变大还是重启了server，它都会工作。
*   Development Mode: 开发模式。
*   Test Mode:    测试模式。
*   Logging:   Rails会详细的记录每次请求的日志信息。开发环境下， Rails记录的信息包含请求的环境，数据库查询和基本的性能等信息。
*   Security:  Rails有效的防范[IP欺诈](http://en.wikipedia.org/wiki/IP_address_spoofing)和[时序攻击](http://en.wikipedia.org/wiki/Timing_attack).
*   Parameter Parsing:  想要指定你的参数是JSON来代替URL编码字符串吗？没问题， Rails会帮你解码JSON成为你可用的参数。
*   Conditional GETs:   Rails可以处理条件GET， 操作请求头并且返回正确的响应头和状态码。你可以使用[stale?](http://api.rubyonrails.org/classes/ActionController/ConditionalGet.html#method-i-stale-3F)来处理全部HTTP详细信息。
*   Caching:   Rails让你方便的配置缓存。
*   HEAD requests:   Rails会透明的把HEAD request转化为GET request， 并只返回header信息。 

#### ActionPack层

* Resourceful Routing:  如果你正在构建一个RESTFul的JSON API， 你要用Rails router,   干净利落方便的帮你从HTTP方法映射到Controller。
* URL Generation:   Routing对应的是URL Generation。 一个好的API是对HTTP方法友好的URLs， 例如[Github gist API](http://developer.github.com/v3/gists/)。
* Header and Redirection Responses:    head :no_content 和 redirect_to_user_url(current_user), 这两方法迟早有用。
* Content Negotiation: Rails的respond_to和respond_with方法，会自动的根据请求的Accept header和可用类型去提供相应的MIME Type。
* Caching:  Rails提供页面， action和片段缓存。 片段缓存有利用构建内嵌的JSON对象。
* Basic, Digest and Token Authentication:  Rails支持这三种类型的HTTP验证。
* Instrumentation: Rails3.0 提供了一个Instrumentation API用于触发一些注册事件，类似于action处理过程， 发送文件或者数据， 重定向和数据查询等事件,  可用于Notifications等功能。
* Generators:  生成器， 对高级别的Rails用户来说没啥用， 但是它可以一键生成相关的资源。
* Plugins:   有好多优秀的Rails第三方插件可以拿来用。

## 基本配置

如果你正在构建一个基本只提供API服务的Rails应用， 那么你可以从一个定制的Rails子集版本开始， 只添加自己需要的特性。

#### 你可以直接生成一个bare rails应用：

    $ rails new my_api  -api

这条命令会帮你生成三条主要的部分：
   
1.  帮你配置包含更少中间件的应用， 尤其是不包含任何和浏览器相关的特性，比如cookie支持等。
2.  使 ApplicationController继承于ApplicationController::API， 而不是ApplicationController::Base.  ApplicationController::API已经剔除了任何包含浏览器相关特性的功能。
3.   配置生成器， 在你生成新的资源的时候，跳过生成views， helpers和assets。

#### 如果你想让已经存在的Rails Application变成一个API App， 那么按下面步骤来做：

** 在config/application.rb中增加下面两行：**
  
    config.middleware.api_only!
    config.generators.api_only!

** 改变app/controller/application_controller.rb 为下列语句： **

{% codeblock application_controller.rb lang:ruby %}
   class ApplicationController < ActionController::API
      # ...
   end
{% endcodeblock %}

##  选择中间件

一个API A
pp应该默认包含下面Middlewares：

*  Rack::Cache:   利用Cache-Control机制来缓存响应。 
*  Rack::Sendfile:   提供文件传输支持。
*  Rack::Lock:  如果你的应用没有标记使用线程安全 (config.threadsafe!), 这个middleware会在你的请求上增加一个互斥锁， 用于保证在任一时刻，只能有一个线程访问改对象。
*  ActionDispatch::RequestId:
*  Rails::Rack::Logger:
*  ActionDispatch::ShowExceptions: 
*  ActionDispatch::DebugExceptions: 
*  ActionDispatch::RemoteIp: IP欺诈保护
*  ActionDispatch::Reloader: 在开发环境下支持code reloading。
*  ActionDispatch::ParamsParser: 根据请求类型Content-Type来解析XML, YAML, JSON。
*  ActionDispatch::Head:  指派HEAD requests为GET requests,并只返回header信息和状态码。
*  Rack::ConditionalGet:  在Rails controller里支持stale?特性
*  Rack::ETag:  自动为所有的字符串响应设置ETag。 主要被用来优化客户端。

#### 你可以通过下面命令来列出你应用里用到的所有Middlewares：

    $ rake middleware

还有一些其他的Middlewares及其说明， 可以查看[原文链接](https://github.com/rails/rails/blob/efd557a60cd976ac17be9e238111a551599caeb5/railties/guides/source/api_app.textile)。