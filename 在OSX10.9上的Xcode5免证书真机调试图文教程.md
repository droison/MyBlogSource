Date: 2014-10-19  
Title: 在OSX10.10上的Xcode6免证书真机调试图文教程   
Published: true  
Category: iOS  
Tags: OSX    
Author: billchai 


所需设备：越狱后的iPhone一台，并且安装AppSync插件  
10.10Yosemete，Xcode6.1，iOS6.1上测试通过，可完美调试无报错
  
## 第一步：制作证书

1）打开  Finder－>应用程序－>实用工具 ->  钥匙串访问   

![pic1](http://y.photo.qq.com/img?s=9oLzWqW03&l=y.jpg)

2）单击："钥匙串访问"选择  "证书" 再选择"创建证书"  
注意：证书名称必须填写为 iPhone Developer（注意大小写）,并且勾选"让我覆盖这些默认值"  
![pic2](http://y.photo.qq.com/img?s=ZCMxmc1my&l=y.jpg)

接下来的步骤一直继续使用默认参数即可.


## 第二步：修改XCode配置文件
注：如遇到文件无法解锁没有权限不能修改，右键复制，然后修改副本文件，修改好更改名字再替换原来的
        plist文件默认会使用Xcode自带的plist编辑器打开,如果无法保存,请用PlistEdit进行plist文件的编辑

1）修改SDKsettings.plist文件
打开Finder，点击任务栏的 前往－>前往文件夹，输入   
		  
	/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdkpropro

打开该目录下的SDKSettings.plist文件（修改2项）  

	CODE_SIGNING_REQUIRED  值改成NO
	ENTITLEMENTS_REQUIRED  值改成NO
![pic3](http://y.photo.qq.com/img?s=mxpPe3w0m&l=y.jpg)
  
2）修改Info.plist文件

	/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/
用XCode打开该目录下的Info.plist文件，右击--Add Row(增加两项)

	PROVISIONING_PROFILE_ALLOWED   值为 NO
	PROVISIONING_PROFILE_REQUIRED  值为 NO
	  
![pic4](http://y.photo.qq.com/img?s=K5EOkJWRA&l=y.jpg)
  
3）将上面Info.plist文件中的 "XCiPhoneOSCodeSignContext" 替换成"XCCodeSignContext",即将其中的”iPhoneOS“字段删除（共3处）  
![pic5](http://y.photo.qq.com/img?s=fFPFkAiFL&l=y.jpg)

## 第三步：设置脚本权限  
打开Finder，点击任务栏的 前往－>前往文件夹，输入   
		
	/Applications/Xcode.app/Contents/Developer/iphoneentitlements

新建gen_entitlements.py文件，内容如下  
  
	#!/usr/bin/env python
	
	import sys
	import struct
	
	if len(sys.argv) != 3:
		print "Usage: %s appname dest_file.xcent" % sys.argv[0]
		sys.exit(-1)
	
	APPNAME = sys.argv[1]
	DEST = sys.argv[2]
	
	if not DEST.endswith('.xml') and not DEST.endswith('.xcent'):
		print "Dest must be .xml (for ldid) or .xcent (for codesign)"
		sys.exit(-1)
	
	entitlements = """
	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
	<plist version="1.0">
	<dict>
	    <key>application-identifier</key>
	    <string>%s</string>
	    <key>get-task-allow</key>
	    <true/>
	</dict>
	</plist>
	""" % APPNAME
	
	f = open(DEST,'w')
	if DEST.endswith('.xcent'):
		f.write("\xfa\xde\x71\x71")
		f.write(struct.pack('>L', len(entitlements) + 8))
	f.write(entitlements)
	f.close()


然后将gen_entitlements.py文件放到/Applications/Xcode.app/Contents/Developer/iphoneentitlements（新建）下
打开终端，输入以下命令（可能需要输入密码,如果没有密码则无法完成此命令,请在用户与群组中新增密码）

	sudo chmod 777 /Applications/Xcode.app/Contents/Developer/iphoneentitlements/gen_entitlements.py
  
## 第四步：修改项目属性 （每次新建项目都要执行）


1）点击项目图标，找到"BuildSettings"选项下的"Code Sign Identity"，将"Debug"下的"Any iOS SDK"子项改为第一步添加的证书"iPhone Developer"

然后点击菜单栏File->New->File->iOS->Resource->Property List 点击Next,将名字设置为Entitlements.plist（有可能已经存在了）  
打开Entitlements.plist文件,添加一个Row,名字为"Can be debugged",Value设置为"YES"  
![pic6](http://y.photo.qq.com/img?s=B91ZbK5t0&l=y.jpg)
  
然后找到"BuildSettings"选项下的"Code Sign Entitlements",将"Debug"下的"Any iOS SDK"子项改为Entitlements.plist  
![pic7](http://y.photo.qq.com/img?s=MBAdNblmL&l=y.jpg)
  
2）添加脚本，切换到Build Phases选项，点击任务栏
  
（这里与Xcode5.0以下版本不同）  
 Editor－>Add Build Phase －> Add Run Script Build Phase

	export
	CODESIGN_ALLOCATE=/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/usr/bin/codesign_allocate
	
	if [ "${PLATFORM_NAME}" =="iphoneos" ] || [ "${PLATFORM_NAME}" == "ipados"]; then
	
	/Applications/Xcode.app/Contents/Developer/iphoneentitlements/gen_entitlements.py "my.company.${PROJECT_NAME}" "${BUILT_PRODUCTS_DIR}/${WRAPPER_NAME}/${PROJECT_NAME}.xcent";
	
	codesign -f -s "iPhone Developer" --entitlements "${BUILT_PRODUCTS_DIR}/${WRAPPER_NAME}/${PROJECT_NAME}.xcent" "${BUILT_PRODUCTS_DIR}/${WRAPPER_NAME}/"
	
	fi
	
![pic8](http://y.photo.qq.com/img?s=OgiQSc1aM&l=y.jpg)

## 第五步：连接设备与调试(iphone/ipad)

关闭一次XCode，插拔一次你的设备，clean一下你的工程，然后选择"Build Settings"-"Code Signing"-"Provisioning profile"，选择一个可用的Provisioning Profile即可调试。

![pic1](http://y.photo.qq.com/img?s=JB7PvlIVW&l=y.jpg)