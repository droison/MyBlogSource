Title: mac中安装maven3.2.2  
Date: 2014-06-13 10:20  
Category: mac  
Tags: mac, maven  
Author: BillChai



	本机OS X：10.9.3，安装XCode，

## 1、下载maven：http://maven.apache.org/

下载了lastest version，文件名：apache-maven-3.2.2-bin.tar.gz

双击解压

将解压后的apache-maven-3.2.2文件夹移到/Users/song/ (~目录)  
即：/usr/local/maven/maven3.2.1

## 2、配置环境变量

	终端中执行 open -e ~/.bash_profile  
	然后输入 
		M3_HOME=/usr/local/maven/maven3.2.1
		PATH=$M3_HOME/bin:$PATH
		export M3_HOME
		export PATH
	保存退出。
## 3、查看是否成功
	新打开一个终端窗口
	通过
		echo $M3_HOME
		echo $PATH
	可查看刚设置的环境变量。

同时，输入   
	mvn -version  
可以看到maven的版本信息了。

### 4、重新设置本地Repository的位置
在maven安装目录的conf目录下，open -e settings.xml  
第54行左右有这么一段：

 	<!-- localRepository
	| The path to the local repository maven will use to store artifacts.
	|
	| Default: ${user.home}/.m2/repository
	<localRepository>/path/to/local/repo</localRepository>
	-->
	在这段之后依样画葫芦加一行
	<localRepository>具体的绝对路径</localRepository>
即可
保存退出。