---
layout: post
title: "ruby全局变量表"
date: 2012-04-24 19:26
comments: true
categories: [Ruby]
---

Ruby全局变量表， 记录。

---

**名字** | **English库名** | **中文释义**                 | **返回值**
:----------- | :-----------: | -----------: | -----------:
-|-|-|-
$0  | $PROGRAM_NAME | 程序名字  | 字符串
$*  |  $ARGV        |  参数值   | 数组
$: | 无 |库加载路径 |数组
$?|$CHILD_STATUS|系统退出时，最后一个子进程的状态|整数
$"|$LOADED_FEATURES|已加载的特性|数组
$$|$PID或$PROCESS_ID|进程号|整数 
$!|$ERROR_INFO|错误信息|字符串
$@|$ERROR_POSITION|错误发生的位置|字符串
$<|$DEFUALT_INPUT|默认输入值设备|对象实例
$>|$DEFAULT_OUTPUT|默认输出设备|对象实例
$;|$FS或$FILED_SEPARATOR|默认字段分隔符，String.split方法会用到|字符串
$,|$OFS或$OUTPUT_FIELD_SEPARATOR或|输入字符串分隔符，连接多个字|符串时用到|字符串
$_|$LAST_READ_LINE|Kernel.puts或Kernel.readline从输入设备中读取的最后一行|字符串
$.|$NR或$INPUT_LINE_NUMBER|当前所读取文件的最后行号|整数
$/|$RS或INPUT_RECORD_SEPARATOR|输入分隔符，即输入结束符|字符串
$\|$OFS或$OUTPUT_RECORD_SEPARATOR |多个Kernel.print或IO.write调用时，各个输出记录的分隔符|字符串
$=|$IGNORECASE|匹配时是否忽略大小写，已经作废|布尔
$&|$MATCH|匹配结果|字符串
$1~$9|无|各组匹配结果|字符串
$~|$LAST_MATCH_INFO|最后一次匹配数据|MatchData实例 
$+|$LAST_PAREN_MATCH|最后一个括号匹配内容|字符串
$`|$PREMATCH|最后一次匹配前的内容|字符串
$'|$POSTMATCH|最后一次匹配后的内容|字符串 


