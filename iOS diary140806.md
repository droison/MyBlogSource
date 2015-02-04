Title: iOS diary140806  
Date: 2014-08-07 10:20  
Category: iOS  
Tags: iOS, uitableview  
Author: BillChai

## 自定义uitableviewcell的宽度，以及一些诡异的问题处理
项目需要，希望uitableview处于页面中中间部分，但滚动指示条依然在整个window的边缘，解决办法无非两种：  
  
1. 把cell做小  
2. 把指示条放远
  
先说第二种方法：发现可以设置uitableview的scrollIndicatorInsets，从而任意改变指示条的位置，但是，这个位置只能在uitableview范围内！  
那就只剩下第一种方法了，比较简单的就是自定义cell，把里面的内容都做一些内边距，这样看起来效果就像居中了～但问题在于，如果如此，cell的点击、删除、编辑等等uitableviewcell默认的一些系统方法展示出来的UI效果，都会是整个宽度的。  
cell做小另一种方法就是修改scrollview的contentinset，因为uitableview继承自uiscrollview，这种方式的问题在于：依然不能改变cell的宽度，cell的宽度会默认设置为uitableview的宽度而不是scrollview的。
最后，采用了网上常用的覆写uitableviewcell的setFrame方法来完成这个工作，实际效果非常好：  
	
	- (void)setFrame:(CGRect)frame {
    	frame.origin.x += ROOT_PORTRAIT_CONTENT_MARGIN_LEFT;
        frame.size.width -= 2 * ROOT_PORTRAIT_CONTENT_MARGIN_LEFT; 
        [super setFrame:frame];
      }
但是在iOS7下，使用该方法会出现一些诡异的问题：

		Probably at least one of the constraints in the following list is one you don't want. Try this: (1) look at each constraint and try to figure out which you don't expect; (2) find the code that added the unwanted constraint or constraints and fix it. (Note: If you're seeing NSAutoresizingMaskLayoutConstraints that you don't understand, refer to the documentation for the UIView property translatesAutoresizingMaskIntoConstraints) 
	(
    	"<NSAutoresizingMaskLayoutConstraint:0xc094560 h=--& v=--& H:[UITableViewWrapperView:0xc68c390(768)]>",
    	"<NSAutoresizingMaskLayoutConstraint:0xbfd4250 h=-&- v=--& TimeLineLCellImg:0xbfa97e0.width == UITableViewWrapperView:0xc68c390.width - 972>"
	)

		Will attempt to recover by breaking constraint 
	<NSAutoresizingMaskLayoutConstraint:0xbfd4250 h=-&- v=--& TimeLineLCellImg:0xbfa97e0.width == UITableViewWrapperView:0xc68c390.width - 972>

通过研究iOS7的修改点，发现现在uitableviewcell的superview是UITableViewWrapperView，而不是uitableview。查看log日志，发现是由于我们在修改frame的时候和设置的AutoresizingMask冲突，虽然最终显示的正确，但是吐出warning日志还是需要处理。  
找到冲突原因，修改就比较容易了，让uitableviewcell的frame为我们自己设置的，而不是采用AutoresizingMask的方式自动生成，因此在setframe中添加代码：   
  
	[self setAutoresizingMask:(UIViewAutoresizingNone)];
注意：不能通过设置[self setTranslatesAutoresizingMaskIntoConstraints:NO]而直接禁止Autoresizing，因为这样必须设置layoutconstraint采用autolayout的方式。通过log看出是将cell的宽度和它的superview的宽度做了autoresizing，只需打断这个设置即可。
  
##iOS根据class name生成class实例：
	NSString *className = @"AbcViewController";
    
    if (NSClassFromString(className)) {

        Class aClass = NSClassFromString(className);
        id instance = [[aClass alloc] init];
        
        if ([instance isKindOfClass:[UIViewController class]]) {
            
            [(UIViewController *)instance setTitle:@"New Title"];
            [self.navigationController pushViewController:(UIViewController *)instance animated:YES];
        }
    }