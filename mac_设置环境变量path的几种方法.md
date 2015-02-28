Date: 2015-02-05  
Title: MAC 设置环境变量path的几种方法  
Published: true  



# MAC 设置环境变量path的几种方法

原文  http://www.flakor.cn/2014-09-14-714.html

mac 一般使用bash作为默认shell

Mac系统的环境变量，加载顺序为：

/etc/profile /etc/paths ~/.bash_profile ~/.bash_login ~/.profile ~/.bashrc

当然/etc/profile和/etc/paths是系统级别的，系统启动就会加载，后面几个是当前用户级的环境变量。

后面3个按照从前往后的顺序读取，如果~/.bash_profile文件存在，则后面的几个文件就会被忽略不读了，如果~/.bash_profile文件不存在，才会以此类推读取后面的文件。

~/.bashrc没有上述规则，它是bash shell打开的时候载入的。

如果没特殊说明,设置PATH的语法都为： 

	#中间用冒号隔开
     export PATH=$PATH:<PATH 1>:<PATH 2>:<PATH 3>:
     
## （一）全局设置 
下面的几个文件设置是全局的，修改时需要root权限

### 1）/etc/paths （全局建议修改这个文件 ）

编辑 paths，将环境变量添加到 paths文件中 ，一行一个路径

Hint：输入环境变量时，不用一个一个地输入，只要拖动文件夹到 Terminal 里就可以了。

### 2）/etc/profile （建议不修改这个文件 ）
全局（公有）配置，不管是哪个用户，登录时都会读取该文件。

### 3）/etc/bashrc （一般在这个文件中添加系统级环境变量） 
全局（公有）配置，bash shell执行时，不管是何种方式，都会读取此文件。

### 4）
1.创建一个文件：

	sudo touch /etc/paths.d/mysql

2.用 vim 打开这个文件（如果是以 open -t 的方式打开，则不允许编辑）：

	sudo vim /etc/paths.d/mysql

3.编辑该文件，键入路径并保存（关闭该 Terminal 窗口并重新打开一个，就能使用 mysql 命令了）

	/usr/local/mysql/bin

据说，这样可以自己生成新的文件，不用把变量全都放到 paths 一个文件里，方便管理。

## （二）单个用户设置

### 1）~/.bash_profile （任意一个文件中添加用户级环境变量）

（注：Linux 里面是 .bashrc 而 Mac 是 .bash_profile）

若bash shell是以login方式执行时，才会读取此文件。该文件仅仅执行一次!默认情况下,他设置一些环境变量

设置命令别名alias ll=’ls -la’

设置环境变量：

	export PATH=/opt/local/bin:/opt/local/sbin:$PATH
### 2）~/.bashrc 同上

如果想立刻生效，则可执行下面的语句：

	$ source 相应的文件

一般环境变量更改后，重启后生效。