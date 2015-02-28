Date: 2014-08-15  
Title:  iOS tips 140815   
Published: true  
Category: iOS   
Author: billchai   



### iOS5下恶心的tableview背景颜色处理：

	if([DeviceInfo isiPadUniversal]&&![DeviceInfo isiOS6plus]){
        [[m_tableViewInfo getTableView] setBackgroundView:nil];
        [[m_tableViewInfo getTableView] setBackgroundView:[[[UIView alloc] init] autorelease]];
        [[m_tableViewInfo getTableView] setBackgroundColor:UIColor.clearColor];
    }