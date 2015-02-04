Date: 2014-09-05  
Title: 新界面调试工具FLEX简单介绍   
Published: true  
Category: iOS  
Tags: iOS,WeChat 
Author: billchai 

# 新界面调试工具FLEX简单介绍
Flipboard最近开源了他们的应用内调试工具，活生生的客户端版的reveal，功能非常强大。github的地址：<https://github.com/Flipboard/FLEX>  

现在我们把这个开源库引入了`br_trunk`，大家可以在`debug`版下使用该工具调试页面。

## 启用FLEX工具步骤

![pic](http://y.photo.qq.com/img?s=8gSIe2132&l=y.jpg)

## 效果

![pic](http://y.photo.qq.com/img?s=yEjquPlRy&l=y.jpg)

未来我们会对这个工具进行维护和一些定制，大家使用过程中如果有问题可以找`billchai`和`ableliang`反馈。


# 功能简介（以下摘自网络）：
Flipboard/FLEX (Flipboard Explorer)是Flipboard发布的应用内调试工具，开发者可在toolbar中查看和修改运行中的应用。

![View Hierarchy Exploration](http://engineering.flipboard.com/assets/flex/basic-view-exploration.gif)


## 功能：
可以在层级中检测和调整视图，可查看每个对象上的属性和变量；  
动态调整任何属性和变量；  
动态调用实例和类方法；  
通过扫描 heap访问任何活跃的对象；  
在app的sandbox中查看文件系统；  
探究应用中所有类和系统框架（公开的和私有的）；  
快速访问有用的对象（比如[UIApplication sharedApplication]）、app委托以及关键窗口的根视图控制器等；  
动态查看和调整 NSUserDefaults 值。
 
与其他工具不同的是FLEX完全在应用中运行，无需连接至LLDB/Xcode，或者不同的远程调试服务器，在真机和模拟器上运行都很好。

## 用法
真的是一键启用:

```
[[FLEXManager sharedManager] showExplorer];
```



## 特性示例
### 调整视图
选中视图，点击工具栏下的“info bar”展示关于视图的更多信息，可以在此调整属性和调用方法。

![View Modification](http://engineering.flipboard.com/assets/flex/advanced-view-editing.gif)

### 堆上的所有对象
FLEX要求为所有的内存块分配内存，你可以在此查看所有的内容。

![Heap Exploration](http://engineering.flipboard.com/assets/flex/heap-browser.gif)

### 文件浏览器
在app的sandbox中查看文件系统，FLEX展示文件大小、图片预览以及 .json 和.plist文件的打印。

![File Browser](http://engineering.flipboard.com/assets/flex/file-browser.gif)

### 探索系统库
开发者可深挖所有公开和私有的内容。你可以为它创建实例和探索它的 Default state 来学习更多内容。

![System Libraries Browser](http://engineering.flipboard.com/assets/flex/system-libraries-browser.gif)

### NSUserDefaults 编辑
FLEX允许你编辑的默认值可以是任意的字符串、数字、数组以及字典的组合。输入被解析为JSON。如果为 defaults key (i.e. NSDate)设置其他类型的对象，那你只能查看但无法编辑。

![NSUserDefaults Editor](http://engineering.flipboard.com/assets/flex/nsuserdefaults-editor.gif)

### 未来计划
Swift运行时内省 (swift classes, swift objects on the heap, etc.)
网络请求日志
通过文件浏览器中文件大小在Search bar中进行过滤和排序
提升对文件类型的检测以及在文件浏览器中展示功能
添加NSUserDefault key/value pairs
等等