---
layout: post
title: "Solr简要指南"
date: 2012-03-07 14:38
comments: true
categories: [Ruby, 全文检索]
---

## 一、 安装

服务器环境， CentOS, 需要的软件有：  jdk 1.6.0 、apache-solr-3.1.0 、apache-tomcat-7.0.26 、 mmseg4j


#### Solr与Sphinx相比较的优势：

   * Solr 可以精确控制对索引进行局部更新，而Sphinx只能全局更新.

   * Solr 可以对几乎任何对象进行索引，该对象甚至可以不是ActiveRecord.而Sphinx和RDBMS耦合过于紧密.

   * Solr 索引的对象ID可以非空或者是字符串，而Sphinx要求被索引对象必须拥有非0整数作为ID.

   * Solr 支持Boolean作为查询条件搜索,更加方便

   * Solr 支持Facets,而Sphinx为此需要做更多工作

   * Solr 是对lucene的包装。所以他可以享受lucene每次升级带来的便利。


#### 安装Java：

    sudo yum install java-1.6.0-openjdk.x86_64

#### 下载Solr和Tomcat

    1. wget  http://labs.renren.com/apache-mirror/tomcat/tomcat-7/v7.0.26/bin/apache-tomcat-7.0.26.tar.gz

    2. wget http://mirror.bjtu.edu.cn/apache/lucene/solr/3.1.0/apache-solr-3.1.0.tgz

    
 下载完之后解压并放到/usr/local下:
 
     tar zxvf apache-solr-3.1.0.tgz
     tar zxvf apache-tomcat-7.0.26.tar.gz
     mv  apache-solr-3.1.0 /usr/local/solr
     mv  apache-tomcat-7.0.26 /usr/local/tomcat

#### 下载mmseg4j， 中文分词用。

     地址： http://code.google.com/p/mmseg4j/downloads/list
 



## 二、 配置

#### 配置环境变量

编辑profile文件：

    sudo vi /etc/profile

添加内容:

    export JAVA_HOME=/usr/lib/jvm/jre-1.6.0
    export CLASSPATH=.:$JAVA_HOME/lib/rt.jar
    export PATH=$PATH:$JAVA_HOME/bin
    export TOMCAT_HOME=/usr/local/tomcat
    export JAVA_OPT="$JAVA_OPTS -Dsolr.solr.home=/usr/local/tomcat/solr_home"


#### 复制solr.war到tomcat/webapps下面
    cp /usr/local/solr/dist/apache-solr-3.1.0.war /usr/local/tomcat/webapps/solr.war

#### 配置solr.xml文件，先创建Catalina/localhost目录：

编辑文件： 

     vi /usr/local/tomcat/conf/Catalina/localhost/solr.xml

添加内容：

    <Context docBase="/usr/local/tomcat/webapps/solr.war" debug="0" crossContext="true">

        <Environment name="solr/home" type="java.lang.String" value="/usr/local/tomcat/solr_home" override="true" />

    </Context>

#### 配置tomcat user

编辑文件：

    vi /usr/local/tomcat/conf/tomcat-users.xml
    
去掉注释块,加入以下内容:

    <role rolename="admin-gui"/>
    <role rolename="admin-script"/>
    <role rolename="manager-gui"/>
    <role rolename="manager-script"/>
    <role rolename="manager-jmx"/>
    <role rolename="manager-status"/>
    <user username="tomcat" password="123456" roles="manager-gui,manager-script,manager-jmx,manager-status,admin-script,admin-gui"/>

**注意： 这里tomcat/123456 是tomcat的用户名，密码， 在你访问tomcat管理页面的时候需要。**


#### 进入tomcat根目录:

创建solr_home文件夹. 然后将/usr/local/solr/example/solr下的conf、data、bin三个文件夹复制到solr_home下;


#### 进入webapps/solr/WEB-INF，打开web.xml，第37行，注释打开，修改如下：

    <env-entry]]]]>  
       <env-entry-name]]]]>solr/home</env-entry-name]]]]>  
       <env-entry-value]]]]>/usr/local/tomcat/solr_home</env-entry-value]]]]>  
       <env-entry-type]]]]>java.lang.String</env-entry-type]]]]>  
     </env-entry]]]]>

#### 解压下载好的mmseg4j-1.8.4.zip:

复制mmseg4j-all-1.8.4.jar到/usr/local/tomcat/webapps/solr/WEB-INF/lib下


#### 进入/usr/local/tomcat/solr_home/conf，打开schema.xml, 在<types></types>之间加入：

    <fieldType name="textComplex" class="solr.TextField" ]]>  
    <analyzer]]>  
        <tokenizer class="com.chenlb.mmseg4j.solr.MMSegTokenizerFactory" mode="complex" dicPath="data/"/>  
    </analyzer]]>  
    </fieldType]]>  
    <fieldType name="textMaxWord" class="solr.TextField" ]]>  
            <analyzer]]>  
               <tokenizer class="com.chenlb.mmseg4j.solr.MMSegTokenizerFactory" mode="max-word"dicPath="data/"/>  
            </analyzer]]>  
    </fieldType]]>  
    <fieldType name="textSimple" class="solr.TextField" ]]>  
            <analyzer]]>  
               <tokenizer class="com.chenlb.mmseg4j.solr.MMSegTokenizerFactory" mode="simple" dicPath="data/"/>  
           </analyzer]]>  
    </fieldType]]>


#### 然后在解压好的mmseg4j文件夹中找到data文件夹

将其内的chars.dic、units.dic、words.dic三个字典文件复制到/usr/local/tomcat/solr_home/data中

#### 启动tomcat

    /usr/local/tomcat/bin/startup.sh

**当然你也可以自己写启动脚本**


#### 检测

访问 http://localhost:8080/manager/status  输入之前的用户名和密码，就可以看到正常的tomcat首页，然后访问http://localhost:8080/solr/admin/analysis.jsp，选择Field下拉选项中的type，后面输入textComplex，然后在Field value中随便复制一段中文文字，点击Analyz，就可以看到mmseg4j的分词结果了。

#### 配置 solrconfig.xml ,   打开 /usr/local/tomcat/solr_admin/conf/solrconfig.xml :

     <dataDir>${solr.data.dir:/usr/local/tomcat/solr_home/data}</dataDir>

添加auto_commit配置， 找到<updateHandler class="solr.DirectUpdateHandler2">位置， 打开注释：

    <autoCommit> 
        <maxDocs>10000</maxDocs>
        <maxTime>1000</maxTime> 
    </autoCommit>

**说明：**

  * maxDocs：当内存索引数量达到指定值的时候，将内存的索引DUMP到硬盘中，并通知searcher类加载新的索引。

  * maxTime:每隔指定的时间段，自动的COMMIT内存中的索引数据，并通知Searcher类加载新的索引。

  * 以上两种方式，以最先达到条件执行为准。

  
## 三、 Rails

#### [Sunspot](http://sunspot.github.com/)
 
#### [sunspot_rails](https://github.com/outoftime/sunspot/tree/master/sunspot_rails)

#### 与Model集成示例：

{% codeblock initializers/devise.rb lang:ruby %}

class Post << ActiveRecord::Base
  has_many :links

  searchable :auto_index => true, :auto_remove => true do
    text :ab  #可以被fulltext或者keywords搜索出来。btw,ywords是fulltext的别名。其实是同一个方法。
    string :location
    integer :blog_id
    boolean :generated   #支持boolean查询
    time :published_at, :stored => true
    text :links do   # 支持这种虚拟属性。
      links.map { |link| link.url + " " + link.title }
    end
  end
end

{% endcodeblock %}

** 请留意auto_index,auto_remove,配置了这两个选项以后，程序就能够自动检测程序，数据是否变得脏了。数据脏又分为dirty?和delete_dirty?,自动方式会自动根据这两种脏脏状态决定是否自动更新索引,还是自动删除。如果这两个选项被设置成false,那么就需要你手动更新索引。 **

#### 增量更新：

** sunspot会将新增、更新的记录发送至solr，但不会发送commit(只有运行reindex才会发送commit命令,  只有commit之后， 索引才会发生变化)， 可以配置 solr/conf/solrconfig.xml ，启用 autoCommit 参数，设置更新的记录数或者时间(ms)， 更新时如果 solr 服务出问题，则会导致rails操作不成功，所以要把更新操作放到resque后台执行， 这里使用[Sunspot-Resque Session Proxy](http://stdout.heyzap.com/2011/08/17/sunspot-resque-session-proxy/) **


#### 关于solr中commit和optimize的概念:

    -    commit

       当你像solr提交索引更新时，只有运行了commit，索引才会发生变化。当然也并不意味着你每次提交都要commit，如果不是那么紧急，你可以多次提交之后，再执行commit操作。

    -   optimize

       optimize有点像硬盘上整理磁盘碎片的操作。为了提高搜索速度，它会将索引重组在一起，然后移除需要被删除删除或是更新的文档，请注意，solr是没有update的这种操作的，只有增加与删除。solr在优化时，将需要删除或是被替换的索引标记为deleted，然后再创建新的文档替换掉需要被替换的。optimize就是执行此操作。所以在优化的时候，你的索引会增大，然后再减小。optimize操作会创建一个全新的的索引结构，所以，你需要预备出2倍于你commit时索引大小的空间。

       
## 参考资料：

 [SolrTomcat Wiki](http://wiki.apache.org/solr/SolrTomcat)
 
 [Episodes 278](http://asciicasts.com/episodes/278-search-with-sunspot)
 
 [Sunspot-Resque Session Proxy](http://stdout.heyzap.com/2011/08/17/sunspot-resque-session-proxy/)