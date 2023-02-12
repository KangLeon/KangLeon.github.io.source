---
title: Objective-C需要记忆的知识点
categories:   
 - iOS
 - 面试、笔试
tags: 
- iOS
- Job Interview
- Written examination
---

# Objective-C需要记忆的知识点

#### 1.类和对象

> * 类是一组具有相同特性和行为的某一类事和物的集合.
> * 对象是类的实例化.

#### 2.property有关语法

> 1. readwrite 是可读可写特征;需要生成getter方法和setter方法.
> 1. readonly 是只读特性  只会生成getter方法 不会生成setter方法.     
> 1. assign 赋值特性,setter方法将传入参数赋值给实例变量.（弱引用）（基本数据类型）       
> 1. retain 表示持有特性,setter方法将传入参数先保留,再赋值,并且变量retaincount+1;（强引用）
> 1. copy   通过copy将对象复制一份,之前的对象会释放.(强引用)          
> 1. nonatomic 表示单线程安全.         
> 1. atomic 表示多线程安全.

#### 3.property相关注意事项：

##### @property后面有哪些修饰符：

> 1. 线程安全的：atomic、nonatomic
> 1. 访问权限的：readonly、readwrite
> 1. 内存管理（ARC）assign、strong、weak、copy
> 1. 内存管理（MRC）assign、retain、release
> 1. 指定方法名称  setter = getter =

##### 详解：

###### 一、线程安全的：多线程存在安全隐患

> 1.一块资源可能会被多个线程共享，也就是多个线程可能同时访问同一资源，
> 
> 
> 如：
> 多线程访问同一个对象，同一个变量，同一个文件
> 
> 2.当多线程访问同一块资源的时候，很容易引发数据错乱和数据安全问题
> ---------原子性--------- ：默认
> 
> 这个属性是为了保证程序在多线程下，编译器会自动生成自旋锁代码，避免该变量的读写不同步问题，提供多线程安全，即多线程中只能有一个线程对它进行访问。
> 
> 注意：
> 
> 1.atomic原子性指的是一个操作不可以被CPU中途暂停，然后再调度。即不能被中断，要么就执行完，要么就不执行
> 2.atomic是自旋锁，当上一线程没有执行完毕的时候（被锁住），下一个线程会一直等待（不会进入睡眠状态），当上一线程任务执行完毕，下一线程立即执行。它区别于互斥锁，互斥锁在等待的时候，会进入睡眠状态，当被上一线程执行完毕后，会被唤醒，然后再执行。
> 3.atomic只给setter方法上锁，getter不会加锁
> 4.atomic需要消耗大量的资源，执行效率低
> ---------非原子性---------
> 
> nonatomic：非原子性，非线程安全，多个线程可以同时对其进行访问，使用该属性编译器会少生成加锁代码，提高性能和效率，使用频率高，一般都是放弃安全，提高性能。

###### 二、访问权限

> * readwrite  默认  拥有getter/setter方法  可读可写
> * readonly  只读属性， 只会生成getter方法，不会生成setter方法

###### 三、内存管理(ARC)

> 1.assign 默认
>   适用于基本数据类型：NSInteger、CGFloat和C数据类型 int、float等
> 
> 2.strong 对应MRC中的retain
> 强引用，只有OC对象才能够使用该属性，它使对象的引用计数加1
> 
> 3.weak
> 弱引用，只是单纯引用某个对象，但是并未拥有该对象
> 即一个对象被持有无数个弱引用，只要没有强引用指向它，那么它就会被清除释放
> 
> 4.copy
> 为减少对上下文的依赖而引入的机制，可以理解为内容的拷贝
> 内容被拷贝后，内存中会有两个存储空间存储相同的内容。指针不是同一个地址
> ---------UI控件使用weak的原因---------：
> UI控件之所以可以使用弱指针，是因为控制器有强指针指向UIView  
> UIView 有强指针指向Subviews数组，数组中由强指针指向控件

 ![](/uploads/images/Objective-C需要记忆的知识点图片/UI控件使用弱饮用.png)

> * 代理必须是weak，因为代理一般都是指向控制器，会造成循环引用，无法释放，造成内存泄露
> * 结论：一般，控件的代理都是控制器，而控制器又拥有着该控件，所以，为了不引发循环引用的问题，代理一般都使用weak；
> ---------关于weak 与assign---------：
> * 在ARC，出现循环引用的时候，必须有一端使用weak
> * weak修饰的对象销毁的时候，指针会自动设置为nil
> * 而assign不会，assign可以用于非OC对象，而weak必须用于OC对象
> ---------关于copy与strong---------：
> 问：NSString、NSArray、NSDictionary常用copy，为什么不用strong？
> strong是强引用，指向的是同一个内存地址，copy是内容拷贝，会另外开辟内存空间，指针指向一个不同的内存地址，copy返回的是一	个不可变对象，如果使用strong修饰可变对象，那么对象就会有可能被不经意间修改，有时不是我们想要的，而copy不会发生这种意外。
> 问：关于ARC下，不显示指定属性关键字时，默认关键字有哪些？
> 1.基本数据类型：atomic    readwrite    assign（多线程安全，可读写，弱引用）
> 2.普通OC对象：   atomic    readwrite    strong（多线程，可读写，强引用）
> ---------关于@property的作用---------：
> 使用@property，编译器会自动为我们添加getter和setter方法。
> 另附一张图：（关于深拷贝和浅拷贝）
![](/uploads/images/Objective-C需要记忆的知识点图片/关于深拷贝和浅拷贝.png)

#### 4.变量作用域

> * @protected 表示只有该类和其子类可以访问该类的变量.
> * @public 表示无论是自身还是子类的对象,还是其它类型的对象均能访问该类的变量.
> * @private 表示只有该类本身的对象可以访问该类的变量.

#### 5.面向对象三大特征:

> * 封装:隐藏内部实现,稳定外部接口.
> * 继承:子类继承父类的所有非私有的方法和属性.
> * 多态:不同类型的对象,对同一方法名的不同响应.
> 注意:
> * 要点一:oc中不允许多继承(一个类有多个父类),但允许多重继承(一个类的父类也拥有其自身的父类).
> * 要点二:一个类调用方法时,先从本类方法找,找不到再从父类找,一直往上直到找到NSObject类中.（方法调用链）

#### 6.self、super、id

> * self:是指向当前对象的指针.
> * super:调用父类的方法.
> * id:是一个指向任何一个继承了NSObject类的对象.

#### 7.开闭原则和里氏替换原则

> (1)开闭原则(OCP Open Closed Principle):
> 
> 核心是:对扩展开放,对修改关闭.改变一个软件时,应该通过扩展的方式来改变软件,而不应该修改					原有的代码来实现变化.
> 
> (2)里氏替换原则(LSP Liskov Substitution Principle):(is A)原则:任何基类可以出现的地方,子类一定可以出现.
> 
> 注意：
> 
> * a.继承必须确保超类所拥有的性质子类中仍然成立.
> * b.子类对象总是可以替换父类对象.

#### 8.内存管理

> (1)黄金法则:凡是对象使用了alloc,new,copy和retain,那么必须有相对应的release和autorelease.
> * 引用计数机制：对象创建后，运行时系统通过对象维护的一个计数器来描述有多少个其他对象在使用自己，当计数器为0时，释放该对象占用的内存空间（该对象调用dealloc方法）。
> (2)深复制和浅复制
> * 浅复制:指针拷贝,只复制指向对象的指针,拷贝后原来对象的引用计数会+1.
> * 深复制:对象拷贝,复制引用对象,重新开辟一块内存,原来对象的引用计数不变.         
> (3)OC采用的是引用计数机制管理对象所占的内存.    
> (4)ARC(automotic reference counting)自动引用计数:它会自动加入内存的控制代码,控制对象的生命周期.隐式的管理内存,编译器会在适当的地方自动插入retain,release和autorelease消息.
> ARC新特征:增加了竖向定义关键字,用strong代替retain,用weak代替assign.
> (5)内存检测等快捷键
> * 释放内存是dealloc.
> * 内存泄露的快捷键:command+shift+b.
> * 内存泄露检测仪:command+I.
> (6)字符串内存管理

 	       //常量字符串,引用计数为-1或无穷大的数
          NSString *str111 =@"kevin";//常量字符串的声明
          
          [str111 retain];//引用计数对常量字符串没有影响
          
          NSLog(@"%ld",[str111 retainCount]);
          
          [str111 release];
          
          //用initWithString创建字符串,引用计数为-1或无穷大的数
          
          NSString *str2 = [[NSString alloc]initWithString:@"alen"];
          
          [str2 retain];
          
          NSLog(@"%ld",[str2 retainCount]);
         
          [str2 release];
          
          [str2 release];
          
          //用initWithFormat创建字符串,引用计数为1(符合引用计数机制)
          NSString *str333 = [[NSString alloc]initWithFormat:@"steven"];
         
          [str333 retain];
          
          NSLog(@"%ld",[str3 retainCount]);
          
          [str333 release];
          
          [str333 release];
> 注意:OC中的引用计数永远不可能为0.
> (7)程序在内存中运行,分几个区?各自用途?
> * a.栈区  由编译器自动分配释放存放函数的参数值,局部变量值等.（比较小，存量有限）
> * b.堆区  一般由程序员手动分配释放,如果不释放可由系统释放.
> * c.数据区 存储全局和静态变量.
> * d.代码区 存放函数体的二进制代码.
(8)引用和指针的区别?
> * 引用是给变量起一个别名,也就是一个变量有两个名字,他们是同一块空间.
> * 指针是分配一块内存用来保存变量的地址,间接引用变量.

#### 9.类别,类拓展,协议

> * 类别(category):为已有的类添加方法.（不能添加实例变量。）（命名方法为：类名+类别名）
> * 类拓展(extension):为类添加私有方法.（不必实现所有方法，可以添加实例变量）
> * 协议(protocol):就是类A不方便实现的方法,委托类B实现.协议有两个修饰词:@required(必须实现的方法),@optional(选择实现的方法)

#### 10.KVC,KVO以及通知中心

> (1)KVC(key-value Coding)键值编码,是一种间接访问对象属性的方法.包括:setVaule: forKey: 和 valueForKey: 两个方法.
> (2)KVO(key-value OBserving)键值监听,它提供了一种监测属性变化的方法.

	//注册监听者
         -(void)addobserver{
         [self addObserver:self forKeyPath:@"name" options:NSKeyValueObservingOptionNew|NSKeyValueObservingOptionOld context:nil];
         }
         
         //监听后返回消息
         -(void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary *)change context:(void *)context{
         NSLog(@"%@ %@",context,change);
         }
         
         //移除监听者
         -(void)dealloc{
         //[self removeObserver:self forKeyPath:@"name"];
         [name release];
         [super dealloc];
         }
       
   
(3)通知(Notification)      
          
         {
           //注册通知
         -(void)registerNotification{
         NSNotification *notification;
         notification = [[NSNotification alloc]initWithName:MEETINGNOTIFICATION object:nil userInfo:nil];
         }

           //添加监听者
         -(void)addObserver:(Workers *)worker{
         [[NSNotificationCenter defaultCenter] addObserver:worker selector:@selector(getNotification) name:MEETINGNOTIFICATION object:nil];
         }
         
           //创建并发送通知
         -(void)postNotification{
         [[NSNotificationCenter defaultCenter] postNotificationName:MEETINGNOTIFICATION object:nil];
         }
         
           //移除监听者
         -(void)removrNotification:(Workers *)worker{
         [[NSNotificationCenter defaultCenter] removeObserver:worker name:MEETINGNOTIFICATION object:nil];
         }
         
> 另外一种实现通知传值的方法（简书）


			第1步：在发送者中实现一个方法进行发送通知。
			NSDicitionary *dict=@{@“color”:coloe,@“userName”:@“haha”};
			
			[[NSNotificationCenter defaultCenter] postNotificationName:@“chandeBgColor” object：nil
											userInfo:dict];
											
			//postNotificationName:之后的参数就是这个通知的名字，要和接受者的名字一样，擦能让接收者			正确接收。
			
			//object:接收对象
			
			//userInfo：携带的参数，在例子中我携带了一个字典，因为有时候我们要传递的参数不只是一个	,所以把东西全部都放在通知里面，在接收着中，根据字典里面的键来取出里面的值。
			
			//在字典中传递的color是一个已经实例化后的对象
			
			第2步：在接收者中注册通知，也就是接收者要进行接收通知，接收通知和发送通知的名字要一致。
			
			[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(changeBgColor:)								name:@“changeBgColor”
					object:nil];
					
			//注意：务必让接收者中name后面的参数和发送者中的name后面参数一样。
			
			第3步：在接收者中实现通知中的方法
			
			//参数类型是NSNotification
			-(void)changeBgcolor:(NSNotification *)notification{
			
			NSLog(@“接收到通知，改变背景颜色”);
			
			//如果是传多个数据，那么需要哪个数据，就对应去除对应的数据即可
			
			self.view.backgroundColor=notification.userIfo[@“color”];
			
			UIlabel *label=(UILabel *)[self.view viewWithTag:100];
	
			label.text=notification.userInfo[@“username”];
	
			}

#### 11. delegate和Notification的区别?

> * delegate针对one-to-one关系，并且reciever可以返回值给sender；
> * notification 可以针对one-to-one/many/none,reciever无法返回值给sender；
> * 所以，delegate用于sender希望接受到reciever的某个功能反馈值，notification用于通			知多个object某个事件

#### 12.可变和不可变的具体意思?

> 不可变得意思是,当一个对象初始化后,它的内存里面存的值不能改变了(调用的时候,一般是把值	复制一份传个另一个变量).可变是,这个对象的内存值是可以改变的.

#### 13.集合类

##### 1.遍历

	//字典遍历
         for (int i = 0; i < [[dict allValues] count]; i++) {
         NSLog(@"%@=%@",[[dict allKeys] objectAtIndex:i],[[dict allValues] objectAtIndex:i]);
         }

	//数组遍历
     方式一：
         for (int index = 0; index < [arr3 count]; index++) {
         NSLog(@"%@",[arr3 objectAtIndex:index]);
         }
         NSLog(@"-------------------------------");
	 方式二：
           快速枚举法
         for (id object in arr1) {
         NSLog(@"%@",object);
         }
>  注意:
>   a.{}表示字典  ()表示数组  {()}表示集
>   b.键是唯一的,一个对象(value)可以对应多个键(key).
>   c.集合是无序的.

##### 2.排序

	//数组排序
         方法一:
         NSArray *arr = @[[NSNumber numberWithInt:23],[NSNumber numberWithInt:16],								[NSNumber numberWithInt:28],[NSNumber numberWithInt:90],								[NSNumber numberWithInt:4]];//新语法(定义)
         NSArray *sortedArr = [arr sortedArrayUsingSelector:@selector(compare:)];
         NSLog(@"%@",sortedArr);

		  方法二:
         Student *stu1 =[[Student alloc]init];
         stu1.score=85;
         Student *stu2 = [[Student alloc]init];
         stu2.score=79;
         Student *stu3 = [[Student alloc]init];
         stu3.score=98;
         NSArray *stuArr =@[stu1,stu2,stu3];
         NSArray *sortedStuArr = [stuArr sortedArrayUsingSelector:
											@selector(compareStudent:)];
         for (int i = 0; i < [sortedStuArr count]; i++) {
         Student *stu = [sortedStuArr objectAtIndex:i] ;
         NSLog(@"%lf",stu.score);
         }
	     //倒序排列:
         -(void)invert{
         for (int i = 0; i < [self count]/2; i++) {
         [self exchangeObjectAtIndex:i withObjectAtIndex:[self count]-i-1];
         }
         }

#### 14.命名常量（Constants）

> 如果要定义一组相关的常量，尽量使用枚举类型（enumerations），枚举类型的命名规则和函数的命名规则相同。
> 建议使用 `NS_ENUM` 和 `NS_OPTIONS` 宏来定义枚举类型，参见官方的 [Adopting Modern Objective-C](https://developer.apple.com/library/ios/releasenotes/ObjectiveC/ModernizationObjC/AdoptingModernObjective-C/AdoptingModernObjective-C.html) 一文：

	//定义一个枚举
	typedef NS_ENUM(NSInteger, NSMatrixMode) {
    NSRadioModeMatrix,
    NSHighlightModeMatrix,
    NSListModeMatrix,
    NSTrackModeMatrix
	};
	//定义bit map
	typedef NS_OPTIONS(NSUInteger, NSWindowMask) {
    NSBorderlessWindowMask      = 0,
    NSTitledWindowMask          = 1 << 0,
    NSClosableWindowMask        = 1 << 1,
    NSMiniaturizableWindowMask  = 1 << 2,
    NSResizableWindowMask       = 1 << 3
	};

	
> 使用`const`定义浮点型或者单个的整数型常量，如果要定义一组相关的整数常量，应该优先使用枚举。常量的命名规范和函数相同：
> const float NSLightGray;
> 不要使用`#define`宏来定义常量，如果是整型常量，尽量使用枚举，浮点型常量，使用`const`定义。`#define`通常用来给编译器决定是否编译某块代码，比如常用的：

     #ifdef DEBUG

> 注意到一般由编译器定义的宏会在前后都有一个`__`，比如*`__MACH__`*。

#### 15.注释中引用参数名或者方法函数名

> 如果在注释中要引用参数名或者方法函数名，使用`||`将参数或者方法括起来以避免歧义：

	// Sometimes we need |count| to be less than zero.

	// Remember to call |StringWithoutSpaces("foo bar baz")|

#### 16.Public API要尽量简洁

> 共有接口要设计的简洁，满足核心的功能需求就可以了。不要设计很少会被用到，但是参数极其复杂的API。如果要定义复杂的方法，使用类别或者类扩展。 

#### 17.\#import和\#include

> -------`#import`是Cocoa中常用的引用头文件的方式，它能自动防止重复引用文件，什么时候使用`#import`，什么时候使用`#include`呢------？
> - 当引用的是一个Objective-C或者Objective-C++的头文件时，使用`#import`
> - 当引用的是一个C或者C++的头文件时，使用`#include`，这时必须要保证被引用的文件提供了保护域（#define guard）。
> 栗子：
> 
> 				#import <Cocoa/Cocoa.h>
> 				#include <CoreFoundation/CoreFoundation.h>
> 				#import "GTMFoo.h"
> 				#include "base/basictypes.h"
> ------为什么不全部使用`#import`呢------？
> 主要是为了保证代码在不同平台间共享时不出现问题。

#### 18.引用框架的根头文件

> 上面提到过，每一个框架都会有一个和框架同名的头文件，它包含了框架内接口的所有引用，在使用框架的时候，应该直接引用这个根头文件，而不是其它子模块的头文件，即使是你只用到了其中的一小部分，编译器会自动完成优化的。

		//正确，引用根头文件
		#import <Foundation/Foundation.h>

		//错误，不要单独引用框架内的其它头文件
		#import <Foundation/NSArray.h>
		#import <Foundation/NSString.h>

#### 19.BOOL的使用

> BOOL在Objective-C中被定义为`signed char`类型，这意味着一个BOOL类型的变量不仅仅可以表示`YES`(1)和`NO`(0)两个值，所以永远**不要**将BOOL类型变量直接和`YES`比较

	//错误，无法确定|great|的值是否是YES(1)，不要将BOOL值直接与YES比较
	BOOL great = [foo isGreat];
	if (great == YES)	
	// ...be great!

	//正确
	BOOL great = [foo isGreat];
	if (great)
	 // ...be great!

> 同样的，也不要将其它类型的值作为BOOL来返回，这种情况下，BOOL变量只会取值的最后一个字节来赋值，这样很可能会取到0（NO）。但是，一些逻辑操作符比如`&&`,`||`,`!`的返回是可以直接赋给BOOL的：

	//错误，不要将其它类型转化为BOOL返回
	- (BOOL)isBold {
	  return [self fontTraits] & NSFontBoldTrait;
	}
	- (BOOL)isValid {
	  return [self stringValue];
	}

	//正确
	- (BOOL)isBold {
	  	return ([self fontTraits] & NSFontBoldTrait) ? YES : NO;
	}

	//正确，逻辑操作符可以直接转化为BOOL
	- (BOOL)isValid {
	   return [self stringValue] != nil;
	}
	- (BOOL)isEnabled {
	   return [self isValid] && [self isBold];
	}

> 另外BOOL类型可以和`_Bool`,`bool`相互转化，但是**不能**和`Boolean`转化。

#### 20.使用ARC

> 除非想要兼容一些古董级的机器和操作系统，我们没有理由放弃使用ARC。在最新版的Xcode(6.2)中，ARC是自动打开的，所以直接使用就好了。
> --------- 在init和dealloc中不要用存取方法访问实例变量-----------
> 当`init``dealloc`方法被执行时，类的运行时环境不是处于正常状态的，使用存取方法访问变量可能会导致不可预料的结果，因此应当在这两个方法内直接访问实例变量。

	//正确，直接访问实例变量
	- (instancetype)init {
	   self = [super init];
	   if (self) {
    	   _bar = [[NSMutableString alloc] init];
      }
      return self;
      }
	- (void)dealloc {
	  [_bar release];
     [super dealloc];
     }

    //错误，不要通过存取方法访问
    - (instancetype)init {
    self = [super init];
    if (self) {
    self.bar = [NSMutableString string];
    }
    return self;
    }
    - (void)dealloc {
    self.bar = nil;
    [super dealloc];
    }

#### 21.保证NSString在赋值时被复制

> `NSString`非常常用，在它被传递或者赋值时应当保证是以复制（copy）的方式进行的，这样可以防止在不知情的情况下String的值被其它对象修改。

	- (void)setFoo:(NSString *)aFoo {
     _foo = [aFoo copy];
    }

#### 22.使用NSNumber的语法糖

> 使用带有`@`符号的语法糖来生成NSNumber对象能使代码更简洁：

    NSNumber *fortyTwo = @42;（会自动完成转换）
    NSNumber *piOverTwo = @(M_PI / 2);
    enum {
     kMyEnum = 2;
    };
    NSNumber *myEnum = @(kMyEnum);

#### 23.nil检查

> 因为在Objective-C中向nil对象发送命令是不会抛出异常或者导致崩溃的，只是完全的“什么都不干”，所以，只在程序中使用nil来做逻辑上的检查。
> 另外，不要使用诸如`nil == Object`或者`Object == nil`的形式来判断。

    //正确，直接判断
    if (!objc) {
	    ...	
    }
	//错误，不要使用nil == Object的形式
    if (nil == objc) {
	    ...	
    }

#### 24.属性的线程安全

> 定义一个属性时，编译器会自动生成线程安全的存取方法（Atomic），但这样会大大降低性能，特别是对于那些需要频繁存取的属性来说，是极大的浪费。所以如果定义的属性不需要线程保护，记得手动添加属性关键字`nonatomic`来取消编译器的优化。

#### 25.点分语法的使用

> 不要用点分语法来调用方法，只用来访问属性。这样是为了防止代码可读性问题。

    //正确，使用点分语法访问属性
    NSString *oldName = myObject.name;
    myObject.name = @"Alice";

    //错误，不要用点分语法调用方法
    NSArray *array = [NSArray arrayWithObject:@"hello"];
    NSUInteger numberOfItems = array.count;
    array.release;

#### 26.Delegate要使用弱引用

> 一个类的Delegate对象通常还引用着类本身，这样很容易造成引用循环的问题，所以类的Delegate属性要设置为弱引用。

    /** delegate */
    @property (nonatomic, weak) id <IPCConnectHandlerDelegate> delegate;




























































