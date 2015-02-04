Date: 2014-09-02 
Title: 对于iPad大字体适配的几个可用的参考方法   
Published: true  
Category: iOS  
Tags: iOS,WeChat  
Author: billchai 

# 对于iPad大字体适配的几个可用的参考方法
根据用户的反馈，普通iPad版控件和字体过小，留白太大，不利于查看，也不利于操作。现在iPad模式下，微信增加了大字体模式，开启模式后，微信的字体和各个View的控件均放大接近2倍，现在对于大字体模式已经适配的大多数界面，效果图如下：
![pic](http://y.photo.qq.com/img?s=dju3z1QIB&l=y.jpg)
因为iPad大字体模式的需求，新开发的页面不仅要适配iPad，还需要能够适配iPad大字体样式，为减少适配困难，我们在之前对于大字体适配过程中对原来一些通用的UI控件进行了又一次的封装，可以很快的将一些常见的控件由iPad普通版适配成大字体样式，下面是这几个控件的介绍和一些注意，希望对适配新的界面有所帮助：
## 一、UIFontExtend
该类大字体样式适配的基础类，此处包含 “字体大小转换” “间距转换” “CGSize转换” "大字体模式判断"。  
使用前需：
  
	#import "UIFontExtend.h"
### 1、字体转换
通用性：通用任何场景  
默认在使用字体时采用系统UIFont中的静态方法生成普通字体和粗体：  
	
>原来我们这样生成字体：  
[UIFont systemFontOfSize:17];    
[UIFont boldSystemFontOfSize:17];  
现在我们这样：  
[UIFont dynamicSystemFontOfSize:17];  
[UIFont dynamicBoldSystemFontOfSize:17];

适配前后对比：  

![pic](http://y.photo.qq.com/img?s=YANFnpCBL&l=y.jpg)

注：如果UI中采用一些特殊字体，非上述系统默认字体，请参考下面间距转换：
### 2、间距转换
即在大字体模式下将原来的距离等比放大。

通用性：基本通用，需要自行校验，如：一些view宽度放大后可能会溢出界面。 

使用方法：

宏 `MMDynamicLen(len)`。 
  
> 原来是这样的：  
UIImageView \*viewIcon = [[UIImageView alloc] initWithImage:uiIcon];  
viewIcon.x = 15;   
现在这样即可：  
UIImageView \*viewIcon = [[UIImageView alloc] initWithImage:uiIcon];  
viewIcon.x = `MMDynamicLen(15)`; 
	
宏 `MMDynamicSize(size)`。
	  
> 原来是这样的：  
UIImageView \*viewIcon = [[UIImageView alloc] initWithImage:uiIcon];  
viewIcon.size = CGSizeMake(30,30);   
现在这样即可：  
UIImageView \*viewIcon = [[UIImageView alloc] initWithImage:uiIcon];  
viewIcon.size = CGSizeMake(30,30);   
viewIcon.size = `MMDynamicSize(viewIcon.size)`;
  
针对1中所说自定义字体，也可采用该方法实现。  
	
>例：使用size为16的Monaco字体  
原来为：UIFont\* font = [UIFont fontWithName:@"Monaco" size:16];  
现改为：UIFont\* font = [UIFont fontWithName:@"Monaco" size:`MMDynamicLen(16)`];

### 3、大字体模式判断
判断当前是否设置为大字体模式.
通用性：通用任何场景

使用方法：

	+ (BOOL)useDynamicSize;
YES为大字体，NO为非大字体
## 二、MMTableViewCellInfo
对MMTableView中的cell进行大字体模式适配，对于通用样式只需要简单传参即可
### 1、通用样式：采用MMTableViewCellInfo的默认makesel方法生成
通用性：暂时没发现问题  
原有init通用MMTableViewCellInfo组件静态方法有7类：
  
	+(MMTableViewCellInfo*) switchCellFor*********
	+(MMTableViewCellInfo*) editorCellFor*********
	+(MMTableViewCellInfo*) normalCellFor*********
	+(MMTableViewCellInfo*) badgeCellFor*********
	+(MMTableViewCellInfo*) urlCellFor*********
	+(MMTableViewCellInfo*) urlInnerBlueCellFor*********
	+(MMTableViewCellInfo*) centerCellFor*********
针对具体展示的view的不同，这7类方法又有16种不同的封装。  
为适配iPad大字体模式，对采用这16种方法生成MMTableViewCellInfo，可以使用两种方式进行适配：  

（1）对init后的cellInfo再进行一次 [MMTableViewCellInfo fitIpadClassic:cellinfo];
		  
	例：    
	MMTableViewCellInfo* favoriteInfo = [MMTableViewCellInfo 
											badgeCellForSel:nil  
											target:nil 
											title:LOCALSTR(@"Favorites_Title") 
											badge:badgeText rightValue:nil
											imageName:@"MoreMyFavorites.png"];  
	[MMTableViewCellInfo fitIpadClassic:favoriteInfo]; //只加一行！ 
	[section addCell:favoriteInfo];
    
（2）直接对原方法增加参数isFitIpadClassic:YES    

	例：
	原来：
	[section addCell:[MMTableViewCellInfo 
						badgeCellForSel:@selector(showFavoriteView)  
						target:self 
						title:LOCALSTR(@"Favorites_Title") 
						badge:badgeText 
						rightValue:nil 
						imageName:@"MoreMyFavorites.png"]];
	适配：
	[section addCell:[MMTableViewCellInfo 
						badgeCellForSel:@selector(showFavoriteView)  
						target:self 
						title:LOCALSTR(@"Favorites_Title") 
						badge:badgeText 
						rightValue:nil 
						imageName:@"MoreMyFavorites.png"
						isFitIpadClassic:YES]];  //只加一句！此处传NO，不做适配
哪怕cell复杂一些也没关系，红点也没关系，适配一键搞定。看图：
![pic](http://y.photo.qq.com/img?s=EB2MK2ZE4&l=y.jpg)
### 2、自定义的makesel实现
自定义makesel需要自己根据“一”中所提及的UIFontExtend中的方法自行适配，但在init MMTableViewCellInfo时注意参数height添加MMDynamicLen()
## 三、MMbadgeView(限有内容的红点)
MMbadgeView新增了两个init方法

>原方法：  
[[MMBadgeView alloc]initWithFrame:CGRectZero];  
适配方法：  
[[MMBadgeView alloc]initIpadClassicWithFrame:CGRectZero];  
特殊适配方法：  
[[MMBadgeView alloc]initIpadClassicWithFrame:CGRectZero andRange:1.2];  
即自定义放大倍数，此处红点文字和大小都为正常大小的1.2倍

## 四、FixTitleColorButton
许多界面使用了FixTitleColorButton做为button，该控件默认不允许修改高度，但在大字体模式下可以。使用时需要在init控件后手动设定高度：
>例：  
UIButton* demoButton = [[StandardButtonProvider genBigRedButton] retain];  
demoButton.size = CGSizeMake(MMDynamicLen(100), `MMDynamicLen(demoButton.height)`);

注意：不要将FixTitleColorButton的autoresizingmask设为UIViewAutoresizingFlexibleHeight，因为在大字体模式下高度可变，会造成按钮高度为0而不可以点击的bug

## 写在最后
新加的页面在是否需要对iPad模式大字体做适配需要看产品的需求，但`已经适配的页面在修改的过程中是一定要注意`的。  
### 现在已经适配的页面包含：


* 登录和注册相关的所有页面  
* 主界面4个tab对应的页面  
* 朋友圈所有相关界面  
* 聊天界面  BaseMsgContentViewController.h  
* 用户详情页 WeixinContactInfoAssist.h  
* 设置相关：  
NewSettingViewController.h  
SettingGeneralViewController.h  
SettingMyAccountInfo.h  
SettingMyProfileViewController.h  
SettingPrivateConfigViewController.h  
SettingAboutMMViewController.h

### 未适配的页面主要有：  
搜索、游戏、表情、收藏、钱包、卡包