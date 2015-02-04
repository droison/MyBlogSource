Title: Mac下 尝试用Pelican和github构建个人博客  
Date: 2014-07-31 17:50  
Category: mac  
Tags: mac, pelican, github  
Author: BillChai
  
	系统环境:OSX10.9.4
	参考教程:http://www.dongxf.com/3_Build_Personal_Blog_With_Pelican_And_GitHub_Pages.html
		    http://x-wei.github.io/pelican_github_blog.html
一直有做一个技术博客的打算，所以风风火火的github page肯定不能错过。非常喜欢markdown语言，平时写文档都是用它的，因为感觉迁移毫无压力，所以决定动手搞一个。  
  
## 1、安装pelican
pelican是什么，麻烦请自己google，python不懂的麻烦看一下基础教程，有好处。

mac下对于pelican的安装非常方便，因为已经集成陪python和pip，先后在命令行下执行以下两条命令：  

	sudo pip install pelican
	sudo pip install Markdown
在这里，我多次安装都失败了，失败后，命令行给提示了log的输出位置，手动查看error log，发现原来是权限问题，需要root权限，所以此处一定要加上sudo，网上很多教程都没有，所以个人需要养成自己解决问题的习惯。
## 2、创建blog（无责任copy）
创建一个 Blog 目录

	mkdir myblog
	cd myblog
快速创建 Blog

	pelican-quickstart
根据提示一步步输入相应的配置项，不知道如何设置的接受默认即可，后续可以通过编辑pelicanconf.py文件更改配置。

以下是生成的目录结构：

	myblog/
	├── content              # 存放输入的源文件
	│   └── (pages)          # 存放手工创建的静态页面
	├── output               # 生成的输出文件
	├── develop_server.sh    # 方便开启测试服务器
	├── Makefile             # 方便管理博客的Makefile
	├── pelicanconf.py       # 主配置文件
	└── publishconf.py       # 发布时使用的配置文件
## 3、写文章
默认写文章的目录在刚刚生成目录中的content目录下，该目录位置可以在pelicanconf.py中进行配置，随意更换。  

在文章的开始需要标记文章的“标题”“作者”“时间”“标签”等信息，添加方法如下
	
	Title: Mac下 尝试用Pelican和github构建个人博客  
	Date: 2014-07-31 17:50  
	Category: mac  
	Tags: mac, pelican, github  
	Author: BillChai
	
	下面就是内容了，你可以使用markdown标记语言进行书写

下面再介绍几个简单的命令  
	  
	make html ------就是把content目录下的md文件解释成html，并输出到output目录下
	make publish ------不仅生成文章的html，同时将套用主题theme
	make serve ------启动一个本地http server，localhost:8000可以直接访问output下的数据
## 4、一键配置，发布到github
由于默认output文件夹默认是没有git的repository的，所以需要添加并绑定到github对应的repository中，添加步骤  
  
	cd output
	git init
	git add .
	git remote add origin https://github.com/droison/droison.github.io
	git pull -u origin master
以上代码在运行时，如果有任何问题，请自行google github使用（此处提供一个链接，[点此][git教程]跳转），中途可能会出现输入github帐号密码的情况。
[git教程]:http://www.happylivelife.com/r/?id=473  
下一步，添加一键发布到github的配置，修改Makefile（注意，python代码要注意缩进）  
  
	publish:
		$(PELICAN) $(INPUTDIR) -o $(OUTPUTDIR) -s $(CONFFILE) $(PELICANOPTS)

	github: publish
		cd $(OUTPUTDIR) ; git add . ;  git commit -am 'your comments' ; git push origin master
如此，每次写完博客，只需 make github，就完成一键上传了
## 5、更换主题
安装主题，比如bootstrap2：

	git clone https://github.com/getpelican/pelican-themes.git
	cd pelican-themes
	pelican-themes -i bootstrap2
选择主题，在pelicanconf.py中添加

	THEME = 'bootstrap2'
## 6、添加图片，添加评论
研究中
## 其它：Code block 红框问题
在提交blog时，markdown语法中的code块对中文出现红框问题，真是恶心了好久，通过查看html源码发现，问题处在一个err的css class中，该css定义在主题文件的pygment.css中，将下面这行注释掉，然后重新导入一遍主题就可以了

	.highlight .err { border: 1px solid #FF0000 } / Error /
根据内容可以看出这是规定书写错误的代码的文本样式，可是平时代码中有的字符也会被识别成错误的代码,所以就造成了代码块中时不时出现恼人的红框的问题，原因清楚了，现在要解决它就很容易了，直接删掉那行中的border样式就可以了。如果你跟我一样完全不需要给这类代码规定特殊样式的话，直接删掉那行即可。  
重新导入主题需要先删除原主题：

	pelican-themes -r bootstrap2
	pelican-themes -i bootstrap2