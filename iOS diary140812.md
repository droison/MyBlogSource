Title: iOS diary140812  
Date: 2014-08-12 11:33  
Category: iOS  
Tags: iOS, GCD  
Author: BillChai

## 利用dispatch_once创建单例
使用Objective-C实现单例模式的最佳方式向来有很多争论，开发者（包括Apple在内）似乎每几年就会改变他们的想法。当Apple引入了Grand Central Dispatch (GCD)（Mac OS 10.6和iOS4.0），他们也引入了一个很适合用于实现单例模式的函数。该函数就是  
	  
	dispatch_once：void dispatch_once( dispatch_once_t *predicate, dispatch_block_t block);
该函数接收一个dispatch_once用于检查该代码块是否已经被调度的谓词（是一个长整型，实际上作为BOOL使用）。它还接收一个希望在应用的生命周期内仅被调度一次的代码块，对于本例就用于shared实例的实例化。  
dispatch_once不仅意味着代码仅会被运行一次，而且还是线程安全的，这就意味着你不需要使用诸如@synchronized之类的来防止使用多个线程或者队列时不同步的问题。  
不多说了，直接上代码：因为app是universal的，需要判断是否是iPad，这个判断多次调用没有意义：  
  
	+ (BOOL) isiPad {
    	static BOOL s_isiPad = NO;
    	
    	static dispatch_once_t onceToken;
    	dispatch_once(&onceToken, ^{
        	NSString *nsModel = [UIDevice currentDevice].model;
        	s_isiPad = [nsModel hasPrefix:@"iPad"];
    	});
    	
    	return s_isiPad;
	}
相应的，单例可以这么实现：  
  
	+ (AccountManager *)sharedManager { 
    	static AccountManager *sharedAccountManagerInstance = nil; 

    	static dispatch_once_t predicate; 
    	dispatch_once(&predicate, ^{       
        	  sharedAccountManagerInstance = [[self alloc] init]; 
    	});
	
    	return sharedAccountManagerInstance; 
	}