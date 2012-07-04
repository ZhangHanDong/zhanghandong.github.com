---
layout: post
title: "bcrypt与密码安全"
date: 2012-01-05 17:30
comments: true
categories: [Ruby, 加密]
---


最近闹的沸沸扬扬的各大网站用户密码泄露事件， 让网民们震惊了，尽然都是明文保存密码。因为存在某些漏洞，比如sql注入， 可能让Hacker拿到全字段的数据列表，如果是明文密码， 那就帮Hacker省事了， Hacker都不需要暴力破解了。

其实对于密码有很多加密手段，但是最安全的莫过于Bcrypt这种算法了， 详细的参看[Coda Hala的这篇文章](http://codahale.com/how-to-safely-store-a-password/), 也有[中文版](http://coolshell.cn/articles/2078.html/comment-page-2#comment-121752)。 其实安全也是相对的， 对于Bcrypt这种算法的优势再于它的慢， 延缓了Hacker暴力破解的时间， 对于用户来说，就相对安全了。

用Rails的同学就有福了。 [devise](https://github.com/plataformatec/devise)默认就是支持Bcrypt的。 

{% codeblock initializers/devise.rb lang:ruby %}
  config.stretches = Rails.env.test? ? 1 : 10
{% endcodeblock %}

这里的stretches被用来做work factor, 数值越大越慢， 如果你发现一个crypt生成值在20毫秒之内，那这个stretches就正好。

另一个措施就是可以在用户输入12345之类的弱密码的时候，禁止其注册，并提示其使用强度比较高的密码。