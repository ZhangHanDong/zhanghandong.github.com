---
layout: post
title: "42个Rails Tips"
date: 2012-05-02 11:37
comments: true
categories: [Ruby, Rails]
---

#### 原文: [From RailsConf 2012 - 10 Things You Didn't Know Rails Could do](http://speakerdeck.com/u/jeg2/p/10-things-you-didnt-know-rails-could-do)

#### 本文适用Rails 版本 3.2.3

---

## Tip 1 : Run From A Single File  可以单文件运行

[source](https://github.com/artemave/thesmallestrailsapp.com)

{% codeblock http://thesmallestrailsapp.com/ lang:ruby %}
%w(action_controller/railtie coderay).map &method(:require)

run TheSmallestRailsApp ||= Class.new(Rails::Application) {
  config.secret_token = routes.append { root to: 'hello#world' }.inspect
  initialize!
}

class HelloController < ActionController::Base
  def world
    render inline: "
      <!DOCTYPE html>
      <title>The Smallest Rails App</title>
      <h3>I am the smallest rails app!</h3>
      <p>Here is my source code:</p>
      #{CodeRay.scan_file(__FILE__, :ruby).div(line_numbers: :table)}
      <a href='https://github.com/artemave/thesmallestrailsapp.com'>Make me smaller</a>
    "
  end
end
{% endcodeblock %}

## Tip 2: Remind you of things  提醒功能，TODO list

{% codeblock users_controller.rb lang:ruby %}
   class UsersController < ApplicationController
     # TODO:  Make it possible to create new users.
   end
{% endcodeblock %}

{% codeblock user.rb lang:ruby %}
  class User < ActiveRecord::Base
    # FIXME: Should token really  be accessible?
    attr_accessible :bil, :email, :name, :token
  end
{% endcodeblock %}

{% codeblock index.html.erb lang:ruby %}
  <%# OPTIMIZE: Paginate this listing. %>
  <%= render Article.all %>
{% endcodeblock %}

**a. 执行命令:** 

    $ rake notes
    
会输出如下结果：

    app/controllers/users_controller.rb:
      * [ 2] [TODO] Make it possible to create new users.
    
    app/models/user.rb:
      * [ 2] [FIXME] Should token really be accessible?
      
    app/views/articles/index.html.erb:
      * [ 1] [OPTIMIZE] Paginate this listing.

**b. 执行命令:**

    $ rake notes:todo
    
会只输出和TODO相关的：
 
    app/controllers/users_controller.rb:
      * [ 2] Make it possible to create new users.

**c. 执行命令:**

    $ rake notes:fixme
  
会只输出和FIXME相关的：

    app/models/user.rb:
      * [ 2] Should token really be accessible?

    
**d. 还可以自定义:**

{% codeblock article.rb lang:ruby %}
  class Article < ActiveRecord::Base
    belongs_to :user
    attr_accessible :body, :subject
    # JEG2: Add that code from your blog here.
  end
{% endcodeblock %}

执行命令：
    
    $ rake notes:custom ANNOTATION=JEG2
    
会输出结果：

    app/models/article.rb:
      * [ 4]Add that code from your blog here.
      
**e. 在TextMate里面你还可以使用TODO 插件**

## Tip 3: Sandbox your Console 使用沙箱控制台

**执行命令:**

    rails c --sandbox
    
使用这个控制台，里面任何的改变都会在你退出之后自动回滚到你未修改前的数据。

**执行命令:** 

    rails r 'p [Article, Comment, User].map(&:count)'
    
rails r 会执行引号里的代码。

## Tip 4: Run Helper Methods in the Console 在控制台运行Helper方法

{% codeblock in the Console lang:ruby %}
   $ rails c
   Loading development environment (Rails 3.2.3)
   >> helper.number_to_currency(100)
   => "$100.00"
   >> helper.time_ago_in_words(3.days.ago)
   => "3 days"
{% endcodeblock %}

## Tip 5: Use Non-WEBrick Servers in Development 在开发中使用非Webrick服务器

{% codeblock Gemfile lang:ruby %}
   source "https://rubygems.org"
   # ...
   
   group :development do
     gem 'thin'
   end
{% endcodeblock %}

执行命令：

    % rails s thin

## Tip 6: Allow you to tab into its configuration 自定义配置

{% codeblock lib/custom/railtie.rb lang:ruby %}
   module Custom
     class Railtie < Rails::Railtie
       config.custom = ActiveSupport::OrderedOptions.new
     end
   end
{% endcodeblock %}

{% codeblock config/application.rb lang:ruby %}
   require_relative "../lib/custom/railtie"
   
   module Blog
     class Application < Rails::Application
       # ...
       config.custom.setting = 42
     end
   end
{% endcodeblock %}

## Tip 7: Keep you Entertained

[RubyDramas](www.rubydramas.com)

## Tip 8: UnderStand Shorthand Migrations 理解便携的迁移文件

    $ rails g resources user name:string email:string token:string bio:text
    
可以简写为：

    $ rails g resources user name email token:string{6} bio:text

生成migrations:

{% codeblock Migrations lang:ruby %}
   class CreateUsers < ActiveRecord::Migration
     def change
       create_table :users do |t|
         t.string :name
         t.string :email
         t.string :token, :limit => 6
         t.text :bio
         t.timestamps
       end
     end
   end
{% endcodeblock %}

## Tip 9: Add Indexes to Migrations 增加索引

    $rails g resource user name:index email:uniq token:string{6} bio:text
    
生成：
{% codeblock Migrations lang:ruby %}
   class CreateUsers < ActiveRecord::Migration
     def change
       create_table :users do |t|
         t.string :name
         t.string :email
         t.string :token, :limit => 6
         t.text :bio
         t.timestamps
       end
       add_index :users, :name
       add_index :users, :email, :unique => true
     end
   end
{% endcodeblock %}

## Tip 10: Add Associations to a Migration 增加关联关系到迁移文件

    $rails g resource article user:references subject body:text
    
生成Migrations文件和model文件：

{% codeblock Migrations lang:ruby %}
   class CreateArticles < ActiveRecord::Migration
     def change
       create_table :articles do |t|
         t.references :user
         t.string :subject
         t.text :body
         t.timestamps
       end
       add_index :articles, :user_id
     end
   end
{% endcodeblock %}


{% codeblock models/article.rb lang:ruby %}
   class Article < ActiveRecord::Base
     belongs_to :user
     attr_accessible :body, :subject
   end
{% endcodeblock %}

还可以使用命令：

    rails g resource comment user:blongs_to article:belongs_to body:text
    
会生成和user、article相关联的model和migration文件。

## Tip 11: Show you the status of the database 显示数据库状态

使用命令：

     $ rake db:migrate:status

输出:

     database: db/development.sqlite3
     
     status   Migration ID    Migration Name
     ---------------------------------------
       up     20120414155612  Create users
       up     20120414160528  Create articles
      down    20120414161355  Create comments

## Tip 12: Import your CSV Data

csv data:

    Name,Email
    James,james@example.com
    Dana,dana@example.com
    Summer,summer@example.com

导入代码：

{% codeblock import csv lang:ruby %}
   require 'csv'
   
   namespace :users do
     desc "Import users from a CSV file"
     task :import => :environment do
       path = ENV.fetch("CSV_FILE") {
         File.join(File.dirname(__FILE__), *%w[.. .. db data users.csv])
       }
       CSV.foreach(path, headers: true, header_converters: :symbol) do |row|
         User.create(row.to_hash)
       end
     end
   end 
{% endcodeblock %}

**注意:**

* headers: true, header_converters: :symbol 这个配置项
* row.to_hash

## Tip 13 : Store CSV in Your Database 在数据库里存储CSV

{% codeblock store csv lang:ruby %}
  class Article <  ActiveRecord::Base
    require 'csv'
    module CSVSerializer
      module_function
      def load(field); field.to_s.parse_csv; end
      def dump(object); Array(object).to_csv; end
    end
    serialize :categories, CSVSerializer
    
    # ...
    
    attr_accessible :body, :subject, :categories
  end
{% endcodeblock %}

## Tip 14: "Pluck" Fields out of your database 从数据库‘Pluck’字段。

    $ rails c
    loading development environment(Rails 3.2.3)
    
    >> User.select(:email).map(&:email)
      User Load(0.1ms) SELECT email FROM "users"
    => ["james@example.com", "dana@example.com", "summer@example.com"]
    >> User.pluck(:email)
       (0.2ms) SELECT email FROM "users"
    => ["james@example.com", "dana@example.com", "summer@example.com"]
    >> User.uniq.pluck(:email)
       (0.2ms) SELECT DISTINCT email FROM "users"
    => ["james@example.com", "dana@example.com", "summer@example.com"]

## Tip 15: Count Records in Groups

    $ rails g resource event article:belongs_to trigger
    
进控制台

    $ rails c
    
    >> article = Article.last
    => #<Article id:1, ...>
    >> {edit:3, view:10}.each do |trigger, count|
    ?>   count.times do
    ?>     Event.new(trigger: trigger).tap{ |e| e.article= article; e.save! }
    ?>   end
    => {:edit => 3, :view => 10}
    >> Event.count
    => 13
    >> Event.group(:trigger).count
    => {"edit" => 3, "view" => 10}

## Tip 16: Allow you to Override Associations

{% codeblock car.rb lang:ruby %}
  class Car < ActiveRecord::Base
    belongs_to :owner
    belongs_to :previous_owner, class_name: "Owner"
    
    def owner=(new_owner)
      self.previous_owner = owner
      super
    end
  end
{% endcodeblock %}

## Tip 17: Instantiate Records without a database 

    $ rails c
    
    >> User.find(1)
    => #<User id: 1, name: "James", ...>
    >> jeg2 = User.instantiate("id" => 1, "email" => "james@example.com")
    => #<User id:1, email:"james@example.com">
    >> jeg2.name = "James Edward Gray II"
    >>jeg2.save!
    => true
    
伪造一条记录。

#TODO .... 
    

