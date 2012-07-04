---
layout: post
title: "SublimeText2 使用指南"
date: 2012-03-14 14:17
comments: true
categories: [Sublime Text 2]
---

#### 引子:

昨天[idecent](blog.irubi.com)向我推荐了一款编辑器 - [Sublime Text 2](http://www.sublimetext.com/)。
经过试用，发现真的不错， 打开项目、文件的速度超快， 让Textmate汗颜， 于是毫不吝惜的把Textmate抛弃了。

**注： SublimeText2是收费的，但不限制使用时间，只是不定时弹出让你付费的提醒。**

---

## 使用指南:

#### 为了方便在终端使用SublimeText2打开文件，需要加Link：

    sudo ln -s /Applications/Sublime\ Text\ 2.app/Contents/SharedSupport/bin/subl /usr/bin/st

## 使用技巧

** 一、启用vim模式 **

* 打开配置文件： Preferences -> Settings User  或者使用快捷键：cmd + ,

* 在配置文件中添加：

        "ignored_packages": []

* 重启Sublime Text2就可以了。

** 二、 Package Control **

Sublime Text 2拥有良好的扩展功能，这就是安装包(Package), 通过安装这个插件： [Sublime Package Control](http://wbond.net/sublime_packages/package_control)

安装方法：
    
* Control + ` 命令调出console.

* 把下面的代码输入，并回车执行。

        import urllib2,os; pf='Package Control.sublime-package'; ipp=sublime.installed_packages_path(); os.makedirs(ipp) if not os.path.exists(ipp) else None; urllib2.install_opener(urllib2.build_opener(urllib2.ProxyHandler())); open(os.path.join(ipp,pf),'wb').write(urllib2.urlopen('http://sublime.wbond.net/'+pf.replace(' ','%20')).read()); print 'Please restart Sublime Text to finish installation'

* 重启Sublime Text2， 如果在 Preferences -> Package Settings中见到Package Control这一项，就说明安装成功了。

** 三、 通过Package Control 来安装其他插件**

* cmd + shift + p  调出命令框

* 输入 install 调出 Package Control: Install Package 选项，按下回车。

* 列表中查找你想安装的插件， 按下回车就会自动安装。 （某些插件是收费的）

* 安装完记得重启Sublime Text 2.

** 推荐插件： 右键菜单增强： SideBarEnhancements **

** 四、 无干扰模式(Distraction Free Mode) **

在 Sublime Text 2 中，只要按下 Control + Shift + Command + F 或是在菜单 View 中选择 Enter Distraction Free Mode 就可以进入这个 UI 最小化模式了。如果是在用 Mac OS X Lion 的话，Sublime Text 2 还同时支持 Lion 的原生全屏模式。

通过修改 “Preferences” -> “File Settings - More” -> “Distraction Free - User” 可以对防干扰模式进行一些设置：

    {
    "line_numbers": false,      //是否显示行号
    "gutter": false,            //是否显示边列
    "draw_centered": true,      //是否居中显示
    "wrap_width": 80,           //换行宽度(单位：字符)
    "word_wrap": true,          //是否自动换行
    "scroll_past_end": true     //滚动能否超过结尾
    }

** 五、 Textmate Bundle **

Sublime Text 2 很给力的一点就是它同时支持一些 TextMate 的 Bundle。

Sublime Text 2 对于 TextMate Bundle 中的 Snippet 有着良好的原生支持，把 Bundle 放到 Packages 目录里就能用。但对 Bundle 中的 command 并不支持.

我自己没有在Sublime Text2中使用textmate的bundle。

** 六、 安装主题 **

可以使用Package Control来查找Theme，选择主题安装， 但是安装完之后，需要看你下载的theme插件的Readme来进行配置。

** 七、 快捷键 **

Navigation

    * Command palette: Command + Shift + P   
    *  Fuzzy finder: Command + P
    *  Function crawling: Command + R
    *  Go to line number: Control + G
    *  Find in files: Command + Shift + F

Text manipulation

    * Replace in file: Command + Alt + F (Control + H on Windows)
    * Select current line: Command + L
    * Duplicate line: Command + Shift + D
    * Multi-select words: Command + D, multiple times
    * Swipe lines: Command + Shift + L, or Control + Shift + Up/Down
    * Join lines: Command + J
    * Delete to beginning of word: Control + Backspace
    * Delete to end of word: Control + Delete

Coding

    * Edit occurrences : Command + Control + G (Alt + F3 on Windows)
    * Auto-align assignments: Command + Shift + A
    * Select scope: Command + Shift + Space, multiple times
    * Jump to matching bracket: Control + M
    * Toggle comment: Command + /


