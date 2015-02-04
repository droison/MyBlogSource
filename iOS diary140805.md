Date: 2014-08-05  
Title: iOS Diary1   
Published: true  
Category: iOS  
Tags: UiTableView, UiTableViewCell    
Author: billchai  

### iOS7处理UiTableview转屏时定位的问题
#### 首先，要先确认下uitableview在转屏过程中先后回调了哪些接口：    

	1、系统首先调用了uiviewcontroller的
		- (void)willRotateToInterfaceOrientation:duration:
	2、之后调用了uitableview的
		- (CGFloat)tableView:heightForRowAtIndexPath:
		用以确认每个cell的高度
	3、在调用uitableview的tableView:heightForRowAtIndexPath:过程中，uitableview又一一调用了对应cell的setFrame方法，以修改对应的高度。
		同时，由于frame的变化，使得每个cell分别调用了其中的layoutSubviews方法。
	4、uitableview继承自uiscrollview，在转屏后会保证contentoffset位置不变，所以，在第3步，uitableview同时计算了转屏后会显示的cell，如果cell在转屏前的页面不存在，系统会调用uitableview的
		- (UITableViewCell *)tableView:cellForRowAtIndexPath:
	5、调起转屏动画，系统会调用uiviewcontroller的
		  -(void)willAnimateRotationToInterfaceOrientation:duration:
		和-(void)didRotateFromInterfaceOrientation:
#### 两种不太好的解决方式
项目需要，uitablevlew在横竖屏下的cell的宽度和高度均有变化，为了保证在转屏后，用户看到的cell依旧可见，先后采用了以下方法：  
	  
1. 转屏前（第1步）记住contentoffset，转屏后（第5步）再通过转屏前后cell高度的不同计算新的offset，然后设置uitableview的contentoffset
2. 转屏前（第1步）记住第一个cell的indexpath，转屏后（第5步）滑倒对应位置  

以上两种方法均可以实现转屏定位的效果，但都有问题：  
  
1. 如果在-(void)willAnimateRotationToInterfaceOrientation:duration:中设定位置，则转屏为“空转”，即转动过程中页面是白色的，动画完成后才会加载对应的cell。这是因为系统在第4步已经计算过即将显示的cell，并为其init过了，此时不会调用  
2. 如果在-(void)didRotateFromInterfaceOrientation:中设定位置，则转动过程中的页面UI既不是转动前页面的样式，也不是转动后的样式，而是第3步和第4步中系统计算出来的cell。而且，转完后会出现明显的页面闪动  
  
#### 最终解决办法 
在分析过uiviewcontroller和uitableview在转动过程中经历的几个过程后，明白是需要采用伪装的方式让系统通过它的计算方式（第3、4步）获得到和转屏前相同的cell，这样转屏的动画和结果的定位就都准确了。  
也就是在- (CGFloat)tableView:heightForRowAtIndexPath:这个方法上做文章，想办法让系统计算的横竖屏相同的contentoffset对应相同的indexpath，欺骗完成后，最后再绘制出真实的即可。  
但是：iOS7开始系统竟然已经实现的相应的方法！  

	- (CGFloat)tableView:estimatedHeightForRowAtIndexPath:
这个估计值就是一种欺骗的手段，让页面先伪装一种估计值，cell显示（第4步cellFor）后再从heightFor取精确值，也就是在第1步和第2步之间添加了这个第1.5步！具体步骤如下：  
  
	1.在第1步中添加如下代码，获取预估的高度
	 NSIndexPath* indexPath = [self.m_tableview indexPathForRowAtPoint:self.m_tableview.contentOffset];
    if (indexPath.row > 0) {
        _estimatedHeight = self.m_tableview.contentOffset.y/indexPath.row;
    }
    2.在第1.5步添加
    if (_estimatedHeight==0) {
        return defaultHeight;  //即转屏前row==0
    }else{
        return _estimatedHeight;
    }
### iOS7如何隐藏状态栏 
在viewWillAppear中添加：  
 
	self.navigationController.navigationBar.translucent = NO;
	if ([self respondsToSelector:@selector(setNeedsStatusBarAppearanceUpdate)]) {
		[self prefersStatusBarHidden];
		[self performSelector:@selector(setNeedsStatusBarAppearanceUpdate)];
	}

覆写prefersStatusBarHidden方法  

	- (BOOL)prefersStatusBarHidden
	{
    	return NO;//隐藏为YES，显示为NO
	}