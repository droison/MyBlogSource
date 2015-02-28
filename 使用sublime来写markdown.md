Date: 2015-02-28 08:51:23  
Title:   
Published: true  
Category:   
Tags:  
Author: song

Last modified by:  song
Last Modified time: 2015-02-28 09:37:57
# 使用sublime来写markdown
sublime是一款非常NB的编辑器，同时兼容Mac、linux和Windows，通过插件化可以将其变得非常强大，无所不能。这两天看到身后的domick非常熟练的使用sublime text，感觉非常羡慕，遂学了一点简单的使用，但愿能够长期使用下去。
## 1、增加包管理工具 package control
我在sublime text3下使用网上找的没有成功，遂只贴出来sublime text2的安装方式。

打开控制台『ctrl+`』，然后在console中输入

	import urllib2,os;pf='Package Control.sublime-package';ipp=sublime.installed_packages_path();os.makedirs(ipp) if not os.path.exists(ipp) else None;open(os.path.join(ipp,pf),'wb').write(urllib2.urlopen('http://sublime.wbond.net/'+pf.replace(' ','%20')).read())
安装完成后可以preferences中看到package control工具。

使用『cmd+shift+p』可以调出改工具：

* 输入『list』，选择『package control : list package』可以查看所有通过package control安装的扩展插件。
* 输入『remove』，选择『package control : remove package』可以删除响应的插件
* 输入『install』，可以选择安装

## 2、安装markdown插件
文本编辑中，markdown是我比较常用的，sublime的插件中比较好的markdown为markdown preview（亲测感觉使用时有点卡）。

在package control : install package中选择markdown preview，安装成功后，在preferences--package sets--markdown preview --default中进行设置语法高亮：

	以下两项置为true
	"enable_mathjax": true,
    "enable_highlight": true,
如此，在编辑md文件时，就会出现响应的语法高亮，具体其它使用方法，可以输入『cmd+shift+p』，在弹出框中选择markdown preview开头的那些功能，通过观察，有介绍markdown简易语法的，有导出为html的，有进行html预览的。

## 3、增加头文件
好多文件编辑都需要有头文件，更好的标识文档的时间、作者等等。我在写markdown文件多数需要最后能够push到github进行blog的展示，因此希望能一键添加。

### 安装插件
找到的合适的工具是[file header](http://shiyanhui.github.io/FileHeader/)，安装过程如上。

安装完成后，查看file header的设置，看到了增加header的快捷键"cmd+option+a"，即每次写完md文章可以采用这种方式假如header的模板。也可以使用"cmd+option+n"直接新建一个包含头的文件，这种适用于写一些代码，希望将头部最早加入。

### 下一步，定制相应的header。
插件默认做了一个比较好的python的header，我这里需要的是markdown的header，这个默认没有，所以需要自行加入。

在package control中输入"list"，找到file header插件并跳转到插件的安装目录，在template-head中选择markdown，然后自行添加。所有头都是采用[jinjia2 template](http://jinja.pocoo.org/docs/)，具体使用方法可以自行查看。

下面贴一份我的markdown头：

	Date: {{create_time}}  
	Title:   
	Published: true  
	Category:   
	Tags:  
	Author: {{author}}

	Last modified by:  {{last_modified_by}}
	Last Modified time: {{last_modified_time}}

`last_modified_by`这个参数很强大，可以在每次cmd+s的时候自动更新时间。稍后研究下怎么自动添加Title。

## 后记
暂时只写这么多吧，好多高级的方法都不太会，需要慢慢熟悉。以后慢慢更新。