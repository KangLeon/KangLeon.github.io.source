---
title: iOS很有深度的几道笔试题
categories:   
 - iOS
 - 面试、笔试
tags: 
- iOS
- Job Interview
- Written examination
---

前言：最近帮公司招人，总结了几道很有深度的一线互联网公司的iOS开发面试和笔试题。

## 1.以下代码有什么问题？

	@property(nonatomic,copy)NSMutableArray *mutable_array;

答案：

a.**首先描述清楚**：可变对象的copy和mutableCopy都是深拷贝。不可变对象的copy是浅拷贝，mutablecopy是深拷贝。


b.**对于该题**：如果赋值过来的是NSMutableArray，copy之后是NSArray；如果赋值过来的是NSArray，copy之后是NSArray，由于这里mutable_array被声明为NSMutableArray，所以不可避免的会调用可变数组的一些addObject：，removeObject：等方法，这样操作一个copy之后的NSArray就会导致问题。

## 2.以下代码有什么问题？怎么优化？

	@property(nonatomic, strong)NSArray *array;

答案：

对于有可变子类的类，如：

	 NSString->NSMutableString、NSArray->NSMutableArray、NSDictionary->NSMutableDictionary；

根据面相对象的多态特性，NSArray类型的变量可以指向NSMutableArray类型的变量，这么做就是为了避免这种情况。如果我们使用strong来声明此属性变量，而外部的代码传入了一个NSMutableArray类型的实例。假如此调用者之后要更改了数组的内容，那么此属性将会被更改，这是不愿意看到的，严重会导致crash，如数组下标越界。


**优化方法**：把strong关键字改为copy

## 3.以下代码输出什么内容？

	#import "Mobile.h"
	@interface Phone : Moblie
	
	@end
	@implementation Phone
	-(id)init{
	self=[super init];
	if (self) {
	NSLog(@"%@", NSStringFromClass([self class]));
	NSLog(@"%@", NSStringFromClass([super class]));
	}
	return self;
	}

答案：Phone、Phone
**解析**：因为我们的方法调用都会被转化为消息传递，
通过该方法：

	void objc_msgSend(void /*id self, SEL op, ... */)


就可以知道:

	[self class] <===>objc_msgSend(self, @selector(class))


通过另一个方法：

	void objc_msgSendSuper(void /* struct objc_super *super, SEL op, ... */)

super是一个结构体，结构是：

	struct objc_super {
	__unsafe_unretained id receiver;
	}

里面的receiver就是self。

	[super class]<===>objc_msgSendSuper(super, @selector(class))

所以不管调用对self还是super调用class，他们最终指向的都是self。

## 4. 以下代码输出什么内容？

	{
	int multiplier=6;
	int(^Block)(int)=^int(int num){
	return num*multiplier;
	};
	multiplier=4;
	NSLog(@"result is %d", Block(2));
	}

答案：12

**解析**：因为block具有截获特性，对不同变量的截获特点也不一样：

* 对于基本数据类型的局部变量截获其值；
* 对于对象类型的局部变量连同其所有权修饰符一起截获；
* 以指针形式截获静态局部变量；
* 不截获全局变量，静态全局变量；
* 所以，对于该题钟的变量符合第一种情况，因此其值为12

## 5.以下代码有问题吗？如果有该怎么解决？

	{
	NSMutableArray *array=[NSMutableArray array];
	void(^Block)(void) = ^{
	[array addObject:@123];
	};
	Block();
	}

答案：没有问题

### 如果是下面的代码呢？该怎么解决？

	{
	NSMutableArray *array=nil;
	void(^Block)(void)=^{
	array=[NSMutableArray array];              
	};  
	Block();
	}

答案：会报错。

**解析**：第一种情况下，是在block中对array直接使用；而第二种情况下，是在block中对array进行赋值，直接使用不用加*__block*，赋值需要加*__block*

**修改方法**：
需要在array的声明处添加__block修饰符。

## 6.以下代码输出什么内容？

	{
	__block int multiplier=10;
	_blk=^int(int num){
	return num*multiplier;
	}
	multiplier=6;
	[self executeBlock];
	}
	-(void)executeBlock{
	int result=_blk(4);    
	NSLog(@"reuslt is %d", reuslt);
	}

答案：24

**解析**：因为__block修饰的变量变成了对象。所以在后面执行block的时候，使用的是修改之后的multiplier值。

## 7. 以下代码有循环引用情况吗？引用环是怎么样的？该怎么解决？

	{
	_array=[NSMutableArray arrayWithObject:@"block"];
	_strBlk=^NSString*(NSString * num){
	return  [NSString stringWithFormat: @"helloc_%@", array[0]];     
	};
	
	_strBlk(@"hello");
	}

答案：有循环引用，引用环如下：

![](/uploads/images/比较有深度的面试和笔试题/自引用环.jpg)

解析：因为对于对象类型的局部变量连同其所有权修饰符一起截获，所以_array的关键字strong一同被截获，所以在block中就对当前对象进行了一个强引用，而对象又强引用block，所以造成了循环引用

**解决方法**：

	{
	_array=[NSMutableArray arrayWithObject:@"block"];
	__weak NSArray *weakArray=_array;
	_strBlk=^NSString*(NSString * num){
	return  [NSString stringWithFormat: @"helloc_%@", array[0]];     
	};
	
	_strBlk(@"hello");
	}

## 8.以下代码有循环引用情况吗？引用环是怎么样的？该怎么解决？

	{
	__block MCBlock* blockSelf = self;
	_blk = ^int(int num){
	//var=2
	int result=num*blockSelf.var;
	return result;
	};
	_blk(3);
	}

答案：在MRC下，不会产生循环引用；
在ARC下，会产生循环引用，引起内存泄漏。

**引用环是**：

![](/uploads/images/比较有深度的面试和笔试题/大环.jpg)

**解决办法**：

	{
	__block MCBlock* blockSelf = self;
	_blk = ^int(int num){
	//var=2
	int result=num*blockSelf.var;
	blockSelf=nil;
	return result;
	};
	_blk(3);
	}

## 9.以下代码有什么问题，该如何优化？

	{
	for(int i=0; i<1000000; i++){
	NSString *string=@"Hello";
	}    
	}

答案：浪费内存。

**解析**：对于产生大量临时对象的情况下，可以主动嵌套在一个自动释放中，这样可以优化内存。如果可以回答出：autoreleasepool可以多层嵌套，和其多层嵌套原理则加分。

**优化方法**：

	{
	@autoreleasepool{
	for(int i=0; i<1000000; i++){
	NSString *string=@"Hello";
	}  
	}
	}

## 10.以下代码是否有问题？

	-(void)viewDidLoad{
	dispatch_sync(dispatch_get_main_queue(), ^{
	[self doSomething];
	});
	}

答案：互相等待，死锁。在主队列同步添加任务会造成互相等待。

### 如果是以下代码有问题吗？

	-(void)viewDidLoad{
	//serialQueue是自定义的串行队列
	dispatch_sync(serialQueue, ^{
	[self doSomething];
	});
	}

答案：自定义的串行队列，则不会有问题。

## 12.以下代码的输出内容是怎么样？

	-(void)viewDidLoad{
	NSLog(@"1");
	dispatch_sync(global_queue, ^{
	NSLog(@"2");
	dispatch_sync(global_queue, ^{
	NSlog(@"3");
	});
	NSlog(@"4");
	});
	NSLog(@"5");
	}

答案：12345

## 13.以下代码输出内容是怎么样的？

	-(void)viewDidLoad{
	dispatch_async(global_queue, ^{
	NSLog(@"1");
	[self perfomrSelector: @selector(printLog) withObject: nil afterDelay: 0];
	NSLog(@"3");
	});
	}
	-(void)printLog{
	NSLog(@"2");
	}

答案：1，3

**解析**：考点在perfomrSelector，这个方法是在需要提交到当前线程runloop上面才能执行，但是由于是异步添到全局队列，全局队列线程池里面的线程默认都没有打开runloop的，所以perfomrSelector方法并没有办法执行，所以结果是1，3
