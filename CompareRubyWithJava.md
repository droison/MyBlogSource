Title: JAVA和Ruby的异同  
Date: 2014-07-31 15:00  
Category: ruby  
Tags: java, ruby    
Author: billchai  
 
	由于JavaWeb在开发过程中太重，配置和开发周期比较长，不适合一个比较小的移动互联网项目，决定学习ROR，首先是ruby语言，作为一个只懂java和objective-c的客户端开发，通过比较java和ruby的异同点有利于很好的进行过度。
## 相同点
* 都有内存管理和垃圾回收的机制
* 所有对象都是强类型对象
* 都包含public private 和 protected的方法
* 都能内嵌说明文档，只不过ruby的说明文档叫rdoc。但在代码中的阅读方式和javadoc基本一致

## 不同点
* ruby是解释型语言，不需要编译。java是需要编译才能执行的
* ruby有很多种第三方的图形化编程工具，比如WxRuby,FXRuby等等
* ruby中一段代码（包括class）的结束标志是end，而java使用大括号包起来
* ruby中用require来替换import使用
* ruby中所有成员变量都是私有的，在外部访问必须经过方法
* 在使用ruby的进行方法调用时，其方法的成对括号是不需要写的，当然，写上也没有问题
* ruby中什么都是对象，包括java中的基本数据类型
* ruby中没有静态类型：无static
* ruby中所有的变量名字只是一个标签（label），没有特定的类型关联他们：弱类型？
* 变量初始化不需要定义类型：i.e. ruby : a = [1,2,3]  java: int[] a = {1,2,3}
* 单元测试不需要单独写，只需要调用方法就可以。同时，单元测试甚至可以在你的程序代码运行前，就能发现异常
* 实例化：ruby: foo = Foo.new("hi")      java: foo = new Foo("hi")
* ruby中的构造函数被固定为initialize，而java中的构造函数为class name
* ruby中使用“mixins”代替java中的interface
* 使用YAML替代XML（xml的一个超集）
* 使用nil代替null
* == 和 equals()  与 java中的对应方法意思正好相反，比较是否是同一个对象用equals，比较两个对象的值是否相等用==