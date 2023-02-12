---
title: iOS面试题汇总
categories:   
 - iOS
 - 面试、笔试
tags: 
- iOS
- Job Interview
- Written examination
---

# iOS面试题系列
#### **1.import 跟 #include、@class有什么区别？＃import<> 跟 #import""又什么区别？**

> * #include 和#import其效果相同,都是查询类中定义的行为(方法);
> * #import不会引起交叉编译,确保头文件只会被导入一次；
> * @class 的表明,只定 义了类的名称,而具体类的行为是未知的,一般用于.h 文件；
> * @class 比#import 编译效率更高。
> * 此外@class 和#import 的主要区别在于解决引用死锁的问题。
> * ＃import<> 跟 #import""又什么区别在于，#import<>导入系统头文件，#import""导入项目头文件。

#### **2. 对OC的理解与语言特性**

> * OC作为一门面向对象的语言，自然具有面向对象的语言特性：封装、继承、多态。它既具有静态语言的特性（如C++），又有动态语言的效率（动态绑定、动态加载等）。总体来讲，OC确实是一门不错的编程语言，
> * Objective-C具有相当多的动态特性，表现为三方面：动态类型（Dynamic typing）、动态绑定（Dynamic binding）和动态加载（Dynamic loading）。动态——必须到运行时（run time）才会做的一些事情。
 
> **动态类型**：即运行时再决定对象的类型，这种动态特性在日常的应用中非常常见，简单来说就是id类型。事实上，由于静态类型的固定性和可预知性，从而使用的更加广泛。静态类型是强类型，而动态类型属于弱类型，运行时决定接受者。
>  
> **动态绑定**：基于动态类型，在某个实例对象被确定后，其类型便被确定了，该对象对应的属性和响应消息也被完全确定。
> 
> **动态加载**：根据需求加载所需要的资源，最基本就是不同机型的适配，例如，在Retina设备上加载@2x的图片，而在老一些的普通苹设备上加载原图，让程序在运行时添加代码模块以及其他资源，用户可根据需要加载一些可执行代码和资源，而不是在启动时就加载所有组件，可执行代码可以含有和程序运行时整合的新类。

#### 3. 简述内存管理基本原则
> * 之前：OC内存管理遵循“谁创建，谁释放，谁引用，谁管理”的机制，当创建或引用一个对象的时候，需要向她发送alloc、copy、retain消息，当释放该对象时需要发送release消息，当对象引用计数为0时，系统将释放该对象，这是OC的手动管理机制（MRC）。
> * 目前：iOS 5.0之后引用自动管理机制——自动引用计数（ARC），管理机制与手动机制一样，只是不再需要调用retain、release、autorelease；它编译时的特性，当你使用ARC时，在适当位置插入release和autorelease；它引用strong和weak关键字，strong修饰的指针变量指向对象时，当指针指向新值或者指针不复存在，相关联的对象就会自动释放，而weak修饰的指针变量指向对象，当对象的拥有者指向新值或者不存在时weak修饰的指针会自动置为nil。
> * 如果使用alloc、copy(mutableCopy)或者retian一个对象时,你就有义务,向它发送一条release或者autorelease消息。其他方法创建的对象,不需要由你来管理内存。
> * 向一个对象发送一条autorelease消息,这个对象并不会立即销毁, 而是将这个对象放入了自动释放池,待池子释放时,它会向池中每一个对象发送 一条release消息,以此来释放对象.
> * 向一个对象发送release消息,并不意味着这个对象被销毁了,而是当这个对象的引用计数为0时,系统才会调用dealloc方法,释放该对象和对象本身它所拥有的实例。
> 
> 
>  **其他注意事项：**
> * 如果一个对象有一个_strong类型的指针指向着，这个对象就不会被释放。如果一个指针指向超出了它的作用域，就会被指向nil。如果一个指针被指向nil，那么它原来指向的对象就被释放了。当一个视图控制器被释放时，它内部的全局指针会被指向nil。用法“：不管全局变量还是局部变量用_strong描述就行。
> * 局部变量：出了作用域，指针会被置为nil。
> * 方法内部创建对象，外部使用需要添加_autorelease;
> * 连线的时候，用_weak描述。
> * 代理使用unsafe_unretained就相当于assign；
> * block中为了避免循环引用问题，使用_weak描述；
> * 声明属性时，不要以new开头。如果非要以new开头命名属性的名字，需要自己定制get方法名，如
> * @property(getter=theString) NSString * newString;
> * 如果要使用自动释放池，用@autoreleasepool{}
> * ARC只能管理Foundation框架的变量，如果程序中把Foundation中的变量强制换成COre Foundation中的变量需要交换管理权；
> * 在非ARC工程中采用ARC去编译某些类：-fobjc-arc。
> * 在ARC下的工程采用非ARC去编译某些类：-fno-fobjc-arc。

#### **4.如何理解MVC设计模式**
> MVC是一种架构模式，M表示Model，V表示视图View，C表示控制器Controller：
> 
> * Model负责存储、定义、操作数据；
> * View用来展示书给用户，和用户进行操作交互；
> * Controller是Model和View的协调者，Controller把Model中的数据拿过来给View用。Controller可以直接与Model和View进行通信，而View不能和Controller直接通信。View与Controller通信需要利用代理协议的方式，当有数据更新时，Model也要与Controller进行通信，这个时候就要用Notification和KVO，这个方式就像一个广播一样，Model发信号，Controller设置监听接受信号，当有数据更新时就发信号给Controller，Model和View不能直接进行通信，这样会违背MVC设计模式。

#### 5.如何理解MVVM设计模式

> * ViewModel层，就是View和Model层的粘合剂，他是一个放置用户输入验证逻辑，视图显示逻辑，发起网络请求和其他各种各样的代码的极好的地方。说白了，就是把原来ViewController层的业务逻辑和页面逻辑等剥离出来放到ViewModel层。
> * View层，就是ViewController层，他的任务就是从ViewModel层获取数据，然后显示。

#### 6.Objective-C 中是否支持垃圾回收机制？
> * OC是支持垃圾回收机制的(Garbage collection简称GC),但是apple的移动终端中,是不支持GC的,Mac桌面系统开发中是支持的.
> * 移动终端开发是支持ARC（Automatic Reference Counting的简称）,ARC是在IOS5之后推出的新技术,它与GC的机制是不同的。我们在编写代码时, 不需要向对象发送release或者autorelease方法,也不可以调用delloc方法,编译器会在合适的位置自动给用户生成release消息(autorelease),ARC 的特点是自动引用技术简化了内存管理的难度.

#### 7.协议的基本概念和协议中方法默认为什么类型
> * OC中的协议是一个方法列表,且多少有点相关。它的特点是可以被任何类使用(实现),但它并不是类(这里我们需要注意),自身不会实现这样方法, 而是又其他人来实现协议经常用来实现委托对象(委托设计模式)。如果一个类采用了一个协议,那么它必须实现协议中必须需要实现的方法,在协议中的方法默认是必须实现(@required),添加关键字@optional,表明一旦采用该协议,这些“可选”的方法是可以选择不实现的。

#### 8.简述类目category优点和缺点
> **优点：**
> 
> * 不需要通过增加子类而增加现有类的行为(方法),且类目中的方法与原始类方法基本没有区别;
> * 通过类目可以将庞大一个类的方法进行划分,从而便于代码的日后的维护、更新以及提高代码的阅读性;
> 
> **缺点：**
> 
> * 无法向类目添加实例变量,如果需要添加实例变量,只能通过定义子类的方式;
> * 类目中的方法与原始类以及父类方法相比具有更高优先级,如果覆盖父类的方法,可能导致super消息的断裂。因此,最好不要覆盖原始类中的方法。

#### 9.类别的作用

> * 给系统原有类添加方法，不能扩展属性。如果类别中方法的名字跟系统的方法名一样，在调用的时候类别中的方法优先级更高；
> * 分散类的实现：如：
 
	 	 	+(NSIndexPath *)indexPathForRow:(NSInteger)row
	  	 inSection:(NSInteger)section
 
> 原本属于NSIndexPath的方法，但因为这个方法经常使用的表的时候调用、跟表的关系特别密切，因此把这个方法一类别的形式、声明在UITableView.h中。
> * 声明私有方法，某一个方法只实现，不声明，相当于私有方法。
> * 类别不能声明变量，类别不可以直接添加属性。property描述setter方法，就不会报错。

#### 10.循环引用的产生原因，以及解决方法

> * 产生原因：如下图所示，对象A和对象B相互引用了对方作为自己的成员变量，只有自己销毁的时候才能将成员变量的引用计数减1。对象A的销毁依赖于对象B的销毁，同时对象B销毁也依赖与对象A的销毁，从而形成循环引用，此时，即使外界没有任何指针访问它，它也无法释放。
> 
> 	![](/uploads/images/iOS面试题汇总所用图片/循环引用示例图.png)
> 
> 								循环引用示例图
> 
> 多个对象间依然会存在循环引用问题，形成一个环，在编程中，形成的环越大越不容易察觉，如下图所示：
> 
> ![](/uploads/images/iOS面试题汇总所用图片/多个对象引用示例图.png)
> 
> 								多个对象引用示例图
> 
> **解决方法：**
> 
> * 事先知道存在循环引用的地方，在合理的位置主动断开一个引用，是对象回收；
> * 使用弱引用的方法。

#### 11.键路径(keyPath)、键值编码（KVC）、键值观察（KVO）

> **键路径**
> 
> * 在一个给定的实体中,同一个属性的所有值具有相同的数据类型。
> * 键-值编码技术用于进行这样的查找—它是一种间接访问对象属性的机制。 - 键路径是一个由用点作分隔符的键组成的字符串,用于指定一个连接在一起的对象性质序列。第一个键的性质是由先前的性质决定的,接下来每个键的值也是相对于其前面的性质。
> * 键路径使您可以以独立于模型实现的方式指定相关对象的性质。通过键路径,您可以指定对象图中的一个任意深度的路径,使其指向相关对象的特定属性。
> 
> **键值编码KVC**
> 
> * 键值编码是一种间接访问对象的属性使用字符串来标识属性，而不是通过调用存取方法，直接或通过实例变量访问的机制，非对象类型的变量将被自动封装或者解封成对象，很多情况下会简化程序代码；
> * KVC的缺点：一旦使用 KVC 你的编译器无法检查出错误,即不会对设置的键、键路径进行错误检查,且执行效率要低于合成存取器方法和自定的 setter 和 getter 方法。因为使用 KVC 键值编码,它必须先解析字符串,然后在设置或者访问对象的实例变量。
> 
> **键值观察KVO**
> 
> * 键值观察机制是一种能使得对象获取到其他对象属性变化的通知 ，极大的简化了代码。
> * 实现 KVO 键值观察模式,被观察的对象必须使用 KVC 键值编码来修改它的实例变量,这样才能被观察者观察到。因此,KVC是KVO的基础。
> 
> **Demo**
> 
> 比如我自定义的一个button
 
	 	[self addObserver:self forKeyPath:@"highlighted" options:0 context:nil];  
	 	
	 	//KVO
	 	
	 	-(void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary *)change context:(void *)context 
	 	{ 
	 	     if ([keyPath isEqualToString:@"highlighted"] ) { 
	 	      [self setNeedsDisplay]; 
	 	} 
	 	  }

> 对于系统是根据keypath去取的到相应的值发生改变，理论上来说是和kvc机制的道理是一样的。
> 
> **KVC机制通过key找到value的原理**
> 
> * 当通过KVC调用对象时，比如：[self valueForKey:@”someKey”]时，程序会自动试图通过下面几种不同的方式解析这个调用。
> * 首先查找对象是否带有 someKey 这个方法，如果没找到，会继续查找对象是否带有someKey这个实例变量（iVar），如果还没有找到，程序会继续试图调用 -(id) valueForUndefinedKey:这个方法。如果这个方法还是没有被实现的话，程序会抛出一个NSUndefinedKeyException异常错误。
> * 补充：KVC查找方法的时候，不仅仅会查找someKey这个方法，还会查找getsomeKey这个方法，前面加一个get，或者_someKey以_getsomeKey这几种形式。同时，查找实例变量的时候也会不仅仅查找someKey这个变量，也会查找_someKey这个变量是否存在。
> * 设计valueForUndefinedKey:方法的主要目的是当你使用-(id)valueForKey方法从对象中请求值时，对象能够在错误发生前，有最后的机会响应这个请求。

**12.在 Objective-C 中如何实现 KVO**

> * 注册观察者(注意：观察者和被观察者不会被保留也不会被释放)
 
      -(void)addObserver:(NSObject *)observer forKeyPath:(NSString *)keyPath 
     options:(NSKeyValueObservingOptions)options 
     context:(void *)context; 
 
> * 接收变更通知

    -(void)observeValueForKeyPath:(NSString *)keyPath
    ofObject:(id)object change:(NSDictionary *)change context:(void )context;
 
> * 移除对象的观察者身份
 
  
	  -(void)removeObserver:(NSObject *)observer
	  forKeyPath:(NSString *)keyPath;
  
> * KVO中谁要监听谁注册，然后对响应进行处理，使得观察者与被观察者完全解耦。KVO只检测类中的属性，并且属性名都是通过NSString来查找，编译器不会检错和补全，全部取决于自己。

#### 13.代理的作用

> * 代理又叫委托，是一种设计模式，代理是对象与对象之间的通信交互，代理解除了对象之间的耦合性。
> * 改变或传递控制链。允许一个类在某些特定时刻通知到其他类，而不需要获取到那些类的指针。可以减少框架复杂度。
> * 另外一点，代理可以理解为java中的回调监听机制的一种类似。
> * 代理的属性常是assign的原因：防止循环引用,以至对象无法得到正确的释放。

#### 14.NSNotification、Block、Delegate和KVO的区别

> * 代理是一种回调机制，且是一对一的关系，通知是一对多的关系，一个对向所有的观察者提供变更通知；
> * 效率：Delegate比NSNOtification高；
> * Delegate和Block一般是一对一的通信；
> * Delegate需要定义协议方法，代理对象实现协议方法，并且需要建立代理关系才可以实现通信；
> * Block：Block更加简洁，不需要定义繁琐的协议方法，但通信事件比较多的话，建议使用Delegate；

#### 15.Objective-C中可修改和不可以修改类型

> * 可修改不可修改的集合类，就是可动态添加修改和不可动态添加修改。
> * 比如NSArray和NSMutableArray,前者在初始化后的内存控件就是固定不可变的，后者可以添加等，可以动态申请新的内存空间

#### 16.当我们调用一个静态方法时,需要对对象进行release吗?

> * 不需要,静态方法(类方法)创建一个对象时,对象已被放入自动释放池。在自动释放池被释放时,很有可能被销毁。

#### 17.当我们释放我们的对象时,为什么需要调用[super dealloc]方法,它的位置又是如何的呢?

> * 因为子类的某些实例是继承自父类的,因此需要调用[super dealloc]方法, 来释放父类拥有的实例,其实也就是子类本身的。一般来说我们优先释放子类拥 有的实例,最后释放父类所拥有的实例。

#### 18.对谓词的认识

> * Cocoa 中提供了一个NSPredicate的类,该类主要用于指定过滤器的条件, 每一个对象通过谓词进行筛选,判断条件是否匹配。如果需要了解使用方法，请看谓词的具体使用.

#### 19.static、self、super关键字的作用

> * 函数体内static变量的作用范围为该函数体，不同于auto变量，该变量的内存只被分配一次，因此其值在下次调用时仍维持上次的值.
> * 在模块内的 static 全局变量可以被模块内所用函数访问，但不能被模块外其它函数访问.
> * 在模块内的static函数只可被这一模块内的其它函数调用，这个函数的使用范围被限制在声明.
> * 在类中的static成员变量属于整个类所拥有，对类的所有对象只有一份拷贝.
> * self:当前消息的接收者。
> * super:向父类发送消息。

#### 20.@public、@protected、@private 它们的含义与作用

> * @public:对象的实例变量的作用域在任意地方都可以被访问 ;
> * @protected:对象的实例变量作用域在本类和子类都可以被访问 ;
> * @private:实例变量的作用域只能在本类(自身)中访问 .

#### 21.解释id类型

> * 任意类型对象，程序运行时才决定对象的类型。

#### 22.switch语句if语句区别与联系

> * 均表示条件的判断,switch语句表达式只能处理的是整型、字符型和枚举类型,而选择流程语句则没有这样的限制。但switch语句比选择流程控制语句效率更高。

#### 23.isMemberOfClass 和 isKindOfClass 联系与区别
> * 联系：两者都能检测一个对象是否是某个类的成员
> * 区别：isKindOfClass 不仅用来确定一个对象是否是一个类的成员,也可以用来确定一个对象是否派生自该类的类的成员 ,而isMemberOfClass 只能做到第一点。
> * 举例：如 ClassA派 生 自NSObject 类 , ClassA *a = [ClassA alloc] init];,[a isKindOfClass:[NSObject class]] 可以检查出 a 是否是 NSObject派生类 的成员,但 isMemberOfClass 做不到。

#### 24.iOS开发中数据持久性有哪几种?
**数据存储的核心都是写文件。**

> * 属性列表：只有NSString、NSArray、NSDictionary、NSData可writeToFile；存储依旧是plist文件。plist文件可以存储的7中数据类型：array、dictionary、string、bool、data、date、number。
> * 对象序列化（对象归档）：对象序列化通过序列化的形式，键值关系存储到本地，转化成二进制流。通过runtime实现自动化归档/解档，请参考这个文章。实现NSCoding协议必须实现的两个方法：
* 1.编码（对象序列化）：把不能直接存储到plist文件中得到数据，转化为二进制数据，NSData，可以存储到本地；
* 2.解码（对象反序列化）：把二进制数据转化为本来的类型。
> * SQLite 数据库：大量有规律的数据使用数据库。
> * CoreData ：通过管理对象进行增、删、查、改操作的。它不是一个数据库，不仅可以使用SQLite数据库来保持数据，也可以使用其他的方式来存储数据。如：XML。

**CoreData的介绍：**

> * CoreData是面向对象的API，CoreData是iOS中非常重要的一项技术，几乎在所有编写的程序中，CoreData都作为数据存储的基础。
> * CoreData是苹果官方提供的一套框架，用来解决与对象声明周期管理、对象关系管理和持久化等方面相关的问题。
> * 大多数情况下，我们引用CoreData作为持久化数据的解决方案，并利用它作为持久化数据映射为内存对象。提供的是对象-关系映射功能，也就是说，CoreData可以将Objective-C对象转换成数据，保存到SQL中，然后将保存后的数据还原成OC对象。

**CoreData的特征：**

> * 通过CoreData管理应用程序的数据模型，可以极大程度减少需要编写的代码数量。
> * 将对象数据存储在SQLite数据库已获得性能优化。
> * 提供NSFetchResultsController类用于管理表视图的数据，即将Core Data的持久化存储在表视图中，并对这些数据进行管理：增删查改。
> * 管理undo/redo操纵；
> * 检查托管对象的属性值是否正确。

**Core Data的6成员对象**

> * 1.NSManageObject:被管理的数据记录Managed Object Model是描述应用程序的数据模型，这个模型包含实体（Entity）、特性（Property）、读取请求（Fetch Request）等。
> * 2.NSManageObjectContext：管理对象上下文，持久性存储模型对象，参与数据对象进行各种操作的全过程，并监测数据对象的变化，以提供对undo/redo的支持及更新绑定到数据的UI。
> * 3.NSPersistentStoreCoordinator:连接数据库的Persistent Store Coordinator相当于数据文件管理器，处理底层的对数据文件的读取和写入，一般我们与这个没有交集。
> * 4.NSManagedObjectModel：被管理的数据模型、数据结构。
> * 5.NSFetchRequest：数据请求；
> * 6.NSEntityDescription：表格实体结构，还需知道.xcdatamodel文件编译后为.momd或者.mom文件。

**Core Data的功能**

> * 对于KVC和KVO完整且自动化的支持，除了为属性整合KVO和KVC访问方法外，还整合了适当的集合访问方法来处理多值关系；
> * 自动验证属性（property）值；
> * 支持跟踪修改和撤销操作；
> * 关系维护，Core Data管理数据的关系传播，包括维护对象间的一致性；
> * 在内存上和界面上分组、过滤、组织数据；
> * 自动支持对象存储在外部数据仓库的功能；
> * 创建复杂请求：无需动手写SQL语句，在获取请求（fetch request）中关联NSPredicate。NSPreadicate支持基本功能、相关子查询和其他高级的SQL特性。它支持正确的Unicode编码、区域感知查询、排序和正则表达式；
> * 延迟操作：Core Data使用懒加载（lazy loading）方式减少内存负载，还支持部分实体化延迟加载和复制对象的数据共享机制；
> * 合并策略：Core Data内置版本跟踪和乐观锁（optimistic locking）来支持多用户写入冲突的解决，其中，乐观锁就是对数据冲突进行检测，若冲突就返回冲突的信息；
> * 数据迁移：Core Data的Schema Migration工具可以简化应对数据库结构变化的任务，在某些情况允许你执行高效率的数据库原地迁移工作；
> * 可选择针对程序Controller层的集成，来支持UI的显示同步Core Data在IPhone OS之上，提供NSFetchedResultsController对象来做相关工作，在Mac OS X上我们用Cocoa提供的绑定（Binding）机制来完成的。

#### 25.对象可以被copy的条件

> * 只有实现了NSCopying和NSMutableCopying协议的类的对象才能被拷贝,分为不可变拷贝和可变拷贝,具体区别戳这；
> * NSCopying协议方法为：
 
	     -(id)copyWithZone:(NSZone *)zone {
	     MyObject *copy = [[[self class] allocWithZone: zone] init];
	     copy.username = [self.username copyWithZone:zone];
	     return copy;
	     }
 

#### 26.自动释放池工作原理

> * 自动释放池是NSAutorelease类的一个实例,当向一个对象发送autorelease消息时,该对象会自动入池,待池销毁时,将会向池中所有对象发送一条release消息,释放对象。
> * [pool release]、 [pool drain]表示的是池本身不会销毁,而是池子中的临时对象都被发送release,从而将对象销毁。

#### 27.在某个方法中 self.name = _name，name = _name 它 们有区别吗,为什么?

> * 前者是存在内存管理的setter方法赋值,它会对_name对象进行保留或者拷贝操作
> * 后者是普通赋值
> * 一般来说，在对象的方法里成员变量和方法都是可以访问的，我们通常会重写Setter方法来执行某些额外的工作。比如说，外部传一个模型过来，那么我会直接重写Setter方法，当模型传过来时，也就是意味着数据发生了变化，那么视图也需要更新显示，则在赋值新模型的同时也去刷新UI。

#### 28.解释self = [super init]方法

> * 容错处理,当父类初始化失败,会返回一个nil,表示初始化失败。由于继承的关系,子类是需要拥有父类的实例和行为,因此,我们必须先初始化父类,然后再初始化子类

#### 29.定义属性时,什么时候用 assign、retain、copy以及它们的之间的区别
> * assign:普通赋值,一般常用于基本数据类型,常见委托设计模式, 以此来防止循环引用。(我们称之为弱引用).
> * retain:保留计数,获得到了对象的所有权,引用计数在原有基础上加1.
> * copy:一般认为,是在内存中重新开辟了一个新的内存空间,用来 存储新的对象,和原来的对象是两个不同的地址,引用计数分别为1。但是当copy对象为不可变对象时,那么copy 的作用相当于retain。因为,这样可以节约内存空间

#### 30.堆和栈的区别

> * 栈区(stack)由编译器自动分配释放 ,存放方法(函数)的参数值, 局部变量的值等，栈是向低地址扩展的数据结构，是一块连续的内存的区域。即栈顶的地址和栈的最大容量是系统预先规定好的。
> * 堆区(heap)一般由程序员分配释放, 若程序员不释放,程序结束时由OS回收，向高地址扩展的数据结构，是不连续的内存区域，从而堆获得的空间比较灵活。
> * 碎片问题：对于堆来讲，频繁的new/delete势必会造成内存空间的不连续，从而造成大量的碎片，使程序效率降低。对于栈来讲，则不会存在这个问题，因为栈是先进后出的队列，他们是如此的一一对应，以至于永远都不可能有一个内存块从栈中间弹出.
> * 分配方式：堆都是动态分配的，没有静态分配的堆。栈有2种分配方式：静态分配和动态分配。静态分配是编译器完成的，比如局部变量的分配。动态分配由alloca函数进行分配，但是栈的动态分配和堆是不同的，他的动态分配是由编译器进行释放，无需我们手工实现。
> * 分配效率：栈是机器系统提供的数据结构，计算机会在底层对栈提供支持：分配专门的寄存器存放栈的地址，压栈出栈都有专门的指令执行，这就决定了栈的效率比较高。堆则是C/C++函数库提供的，它的机制是很复杂的。
> * 全局区(静态区)(static),全局变量和静态变量的存储是放在一块 的,初始化的全局变量和静态变量在一块区域, 未初始化的全局变量和未初始化的静态变量在相邻的另一块区域。程序结束后有系统释放。
> * 文字常量区—常量字符串就是放在这里的。程序结束后由系统释放。
> * 程序代码区—存放函数体的二进制代码

#### 31.怎样使用performSelector传入3个以上参数，其中一个为结构体

* 因为系统提供的performSelector的API中，并没有提供三个参数。因此，我们只能传数组或者字典，但是数组或者字典只有存入对象类型，而结构体并不是对象类型,我们只能通过对象放入结构作为属性来传过去了.


     -(id)performSelector:(SEL)aSelector;
     -(id)performSelector:(SEL)aSelector withObject:(id)object;
     -(id)performSelector:(SEL)aSelector withObject:
     (id)object1 withObject:(id)object2;

具体实现如下：

     typedef struct HYBStruct {
     int a;
     int b;
     } *my_struct;

----------------------------

     @interface HYBObject : NSObject
 
     @property (nonatomic, assign) my_struct arg3;
 
     @property (nonatomic, copy)  NSString *arg1;
 
     @property (nonatomic, copy) NSString *arg2;
 
     @end
     @implementation HYBObject
 
     // 在堆上分配的内存，我们要手动释放掉
 
     - (void)dealloc {
 
     free(self.arg3);
 
     }
 
     @end

-----------------------

**测试：**
 
     my__struct str = (my_struct)(malloc(sizeof(my_struct)));
 
     str->a = 1;
 
     str->b = 2;
 
     HYBObject *obj = [[HYBObject alloc] init];
 
     obj.arg1 = @"arg1";
 
     obj.arg2 = @"arg2";
 
     obj.arg3 = str;
 
     [self performSelector:@selector(call:) withObject:obj];
 
     // 在回调时得到正确的数据的
 
     -(void)call:(HYBObject *)obj {
 
     NSLog(@"%d %d", obj.arg3->a, obj.arg3->b);
 
     }

#### 32.UITableViewCell上有个UILabel，显示NSTimer实现的秒表时间，手指滚动cell过程中，label是否刷新，为什么？

> 这是否刷新取决于timer加入到Run Loop中的Mode是什么。Mode主要是用来指定事件在运行循环中的优先级的，分为：
> 
> * NSDefaultRunLoopMode（kCFRunLoopDefaultMode）：默认，空闲状态
> * UITrackingRunLoopMode：ScrollView滑动时会切换到该Mode
> * UIInitializationRunLoopMode：run loop启动时，会切换到该mode
> * NSRunLoopCommonModes（kCFRunLoopCommonModes）：Mode集合
> 苹果公开提供的Mode有两个：
 * NSDefaultRunLoopMode（kCFRunLoopDefaultMode）
 * NSRunLoopCommonModes（kCFRunLoopCommonModes）
> * 在编程中：如果我们把一个NSTimer对象以NSDefaultRunLoopMode（kCFRunLoopDefaultMode）添加到主运行循环中的时候, ScrollView滚动过程中会因为mode的切换，而导致NSTimer将不再被调度。当我们滚动的时候，也希望不调度，那就应该使用默认模式。但是，如果希望在滚动时，定时器也要回调，那就应该使用common mode。

#### 33.对于单元格重用的理解
> * 当屏幕上滑出屏幕时，系统会把这个单元格添加到重用队列中，等待被重用，当有新单元从屏幕外滑入屏幕内时，从重用队列中找看有没有可以重用的单元格，若有，就直接用，没有就重新创建一个。

#### 34.解决cell重用的问题
> * UITableView通过重用单元格来达到节省内存的目的，通过为每个单元格指定一个重用标示（reuseidentifier），即指定了单元格的种类，以及当单元格滚出屏幕时，允许恢复单元格以便复用。对于不同种类的单元格使用不同的ID，对于简单的表格，一个标示符就够了。
> * 如一个TableView中有10个单元格，但屏幕最多显示4个，实际上iPhone只为其分配4个单元格的内存，没有分配10个，当滚动单元格时，屏幕内显示的单元格重复使用这4个内存。实际上分配的cell的个数为屏幕最大显示数，当有新的cell进入屏幕时，会随机调用已经滚出屏幕的Cell所占的内存，这就是Cell的重用。
> * 对于多变的自定义Cell，这种重用机制会导致内容出错，为解决这种出错的方法，把原来的
  
     UITableViewCell *cell = [tableview dequeueReusableCellWithIdentifier:defineString]
 
> 修改为：

     UITableViewCell *cell = [tableview cellForRowAtIndexPath:indexPath];
 
> 这样就解决掉cell重用机制导致的问题。

#### 35.有a、b、c、d 4个异步请求，如何判断a、b、c、d都完成执行？如果需要a、b、c、d顺序执行，该如何实现？

> * 对于这四个异步请求，要判断都执行完成最简单的方式就是通过GCD的group来实现：
 
     dispatch_queue_t queue = 
 
     dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
 
     dispatch_group_t group = dispatch_group_create();
 
     dispatch_group_async(group, queue, ^{ /*任务a / });
 
     dispatch_group_async(group, queue, ^{ /任务b / });
 
     dispatch_group_async(group, queue, ^{ /任务c / });
 
     dispatch_group_async(group, queue, ^{ /任务d */ });
 
     dispatch_group_notify(group,dispatch_get_main_queue(), ^{
 
     // 在a、b、c、d异步执行完成后，会回调这里
 
     });
 
> * 当然，我们还可以使用非常老套的方法来处理，通过四个变量来标识a、b、c、d四个任务是否完成，然后在runloop中让其等待，当完成时才退出runloop。但是这样做会让后面的代码得不到执行，直到Run loop执行完毕。
> * 解释：要求顺序执行，那么可以将任务放到串行队列中，自然就是按顺序来异步执行了。

#### 36.使用block有什么好处？使用NSTimer写出一个使用block显示（在UILabel上）秒表的代码

> * 代码紧凑，传值、回调都很方便，省去了写代理的很多代码。
> * NSTimer封装成的block，具体实现
> 
> 实现方法：
> 
     NSTimer *timer = [NSTimer scheduledTimerWithTimeInterval:1.0
 
     repeats:YES
 
     callback:^() {
 
     weakSelf.secondsLabel.text = ...
 
     }
 
     [[NSRunLoop currentRunLoop] addTimer:timer forMode:NSRunLoopCommonModes];

#### 37.一个view已经初始化完毕，view上面添加了n个button，除用view的tag之外，还可以采用什么办法来找到自己想要的button来修改button的值

> 有2种方法解决：
> 
> * 第一种：如果是点击某个按钮后，才会刷新它的值，其它不用修改，那么不用引用任何按钮，直接在回调时，就已经将接收响应的按钮给传过来了，直接通过它修改即可。
> * 第二种：点击某个按钮后，所有与之同类型的按钮都要修改值，那么可以通过在创建按钮时将按钮存入到数组中，在需要的时候遍历查找。

#### 38.线程与进程的区别和联系?

> * 一个程序至少要有进城,一个进程至少要有一个线程.
> * 进程:资源分配的最小独立单元,进程是具有一定独立功能的程序关于某个数据集合上的一次运行活动,进程是系统进行资源分配和调度的一个独立单位.
> * 线程:进程下的一个分支,是进程的实体,是CPU调度和分派的基本单元,它是比进程更小的能独立运行的基本单位,线程自己基本不拥有系统资源,只拥有一点在运行中必不可少的资源(程序计数器、一组寄存器、栈)，但是它可与同属一个进程的其他线程共享进程所拥有的全部资源。
> * 进程和线程都是由操作系统所体会的程序运行的基本单元，系统利用该基本单元实现系统对应用的并发性。
> * 进程和线程的主要差别在于它们是不同的操作系统资源管理方式。进程有独立的地址空间，一个进程崩溃后，在保护模式下不会对其它进程产生影响，而线程只是一个进程中的不同执行路径。线程有自己的堆栈和局部变量，但线程之间没有单独的地址空间，一个线程死掉就等于整个进程死掉，所以多进程的程序要比多线程的程序健壮，但在进程切换时，耗费资源较大，效率要差一些。
> * 但对于一些要求同时进行并且又要共享某些变量的并发操作，只能用线程，不能用进程。

#### 39.多线程编程

> * NSThread:当需要进行一些耗时操作时会把耗时的操作放到线程中。线程同步：多个线程同时访问一个数据会出问题，NSlock、线程同步块、@synchronized(self){}。
> * NSOperationQueue操作队列（不需考虑线程同步问题）。编程的重点都放在main里面，NSInvocationOperation、BSBlockOperation、自定义Operation。创建一个操作绑定相应的方法，当把操作添加到操作队列中时，操作绑定的方法就会自动执行了，当把操作添加到操作队列中时，默认会调用main方法。
> * GCD（``Grand Central Dispatch`）宏大的中央调度，串行队列、并发队列、主线程队列；
> * 同步和异步：同步指第一个任务不执行完，不会开始第二个，异步是不管第一个有没有执行完，都开始第二个。
> * 串行和并行：串行是多个任务按一定顺序执行，并行是多个任务同时执行；
> * 代码是在分线程执行，在主线程嘟列中刷新UI。

 ** 多线程编程是防止主线程堵塞、增加运行效率的最佳方法。**
 
> * Apple提供了NSOperation这个类，提供了一个优秀的多线程编程方法；
> * 一个NSOperationQueue操作队列，相当于一个线程管理器，而非一个线程，因为你可以设置这个线程管理器内可以并行运行的线程数量等。
> * 多线程是一个比较轻量级的方法来实现单个应用程序内多个代码执行路径。
> * iPhoneOS下的主线程的堆栈大小是1M。第二个线程开始就是512KB，并且该值不能通过编译器开关或线程API函数来更改，只有主线程有直接修改UI的能力。

#### 40.定时器与线程的区别;

> * 定时器;可以执行多次，默认在主线程中。
> * 线程：只能执行一次。

#### 41.TCP和UDP的区别于联系

> * TCP为传输控制层协议，为面向连接、可靠的、点到点的通信；
> * UDP为用户数据报协议，非连接的不可靠的点到多点的通信；
> * TCP侧重可靠传输，UDP侧重快速传输。

#### 42.TCP连接的三次握手

> * 第一次握手：客户端发送syn包（syn=j）到服务器，并进入SYN_SEND状态，等待服务器确认；
> * 第二次握手：服务器收到syn包，必须确认客户的SYN（ack=j+1），同时自己也发送一个SYN包，即SYN+ACK包，此时服务器进入SYN+RECV状态；
> * 第三次握手：客户端收到服务器的SYN+ACK包，向服务器发送确认包ACK（ack=k+1），此发送完毕，客户端和服务器进入ESTABLISHED状态，完成三次状态。

#### 43.Scoket连接和HTTP连接的区别：

> * HTTP协议是基于TCP连接的，是应用层协议，主要解决如何包装数据。Socket是对TCP/IP协议的封装，Socket本身并不是协议，而是一个调用接口（API），通过Socket，我们才能使用TCP/IP协议。
> * HTTP连接：短连接，客户端向服务器发送一次请求，服务器响应后连接断开，节省资源。服务器不能主动给客户端响应（除非采用HTTP长连接技术），iPhone主要使用类NSURLSession。
> * Socket连接：长连接，客户端跟服务器端直接使用Socket进行连接，没有规定连接后断开，因此客户端和服务器段保持连接通道，双方可以主动发送数据，一般多用于游戏.Socket默认连接超时时间是30秒，默认大小是8K（理解为一个数据包大小）。

#### 44.HTTP协议的特点，关于HTTP请求GET和POST的区别

 **GET和POST的区别：**
> * HTTP超文本传输协议，是短连接，是客户端主动发送请求，服务器做出响应，服务器响应之后，链接断开。HTTP是一个属于应用层面向对象的协议，HTTP有两类报文：请求报文和响应报文。
>  
> * HTTP请求报文：一个HTTP请求报文由请求行、请求头部、空行和请求数据4部分组成。
>  
> * HTTP响应报文：由三部分组成：状态行、消息报头、响应正文。
>  
> * GET请求：参数在地址后拼接，没有请求数据，不安全（因为所有参数都拼接在地址后面），不适合传输大量数据（长度有限制，为1024个字节）。 
> 
> >    GET提交、请求的数据会附在URL之后，即把数据放置在HTTP协议头<requestline>中。
> >    以？分割URL和传输数据，多个参数用&连接。如果数据是英文字母或数字，原样发送，
> >    如果是空格，转换为+，如果是中文/其他字符，则直接把字符串用BASE64加密。
> 
> * POST请求：参数在请求数据区放着，相对GET请求更安全，并且数据大小没有限制。把提交的数据放置在HTTP包的包体<request-body>中.
>  
> * GET提交的数据会在地址栏显示出来，而POST提交，地址栏不会改变。

> **传输数据的大小：**
> 
> * GET提交时，传输数据就会受到URL长度限制，POST由于不是通过URL传值，理论上书不受限。
> * 安全性：
> 
> **POST的安全性要比GET的安全性高；**
> * 通过GET提交数据，用户名和密码将明文出现在URL上，比如登陆界面有可能被浏览器缓存。
> * HTTPS：安全超文本传输协议（Secure Hypertext Transfer Protocol），它是一个安全通信通道，基于HTTP开发，用于客户计算机和服务器之间交换信息，使用安全套结字层（SSI）进行信息交换，即HTTP的安全版。

#### 45.ASIHttpRequest、AFNetWorking之间的区别

> * ASIHttpRequest功能强大，主要是在MRC下实现的，是对系统CFNetwork API进行了封装，支持HTTP协议的CFHTTP，配置比较复杂，并且ASIHttpRequest框架默认不会帮你监听网络改变，如果需要让ASIHttpRequest帮你监听网络状态改变，并且手动开始这个功能。
> * AFNetWorking构建于NSURLConnection、NSOperation以及其他熟悉的Foundation技术之上。拥有良好的架构，丰富的API及模块构建方式，使用起来非常轻松。它基于NSOperation封装的，AFURLConnectionOperation子类。
> * ASIHttpRequest是直接操作对象ASIHttpRequest是一个实现了NSCoding协议的NSOperation子类；AFNetWorking直接操作对象的AFHttpClient，是一个实现NSCoding和NSCopying协议的NSObject子类。
> * 同步请求：ASIHttpRequest直接通过调用一个startSynchronous方法；AFNetWorking默认没有封装同步请求，如果开发者需要使用同步请求，则需要重写getPath:paraments:success:failures方法，对于AFHttpRequestOperation进行同步处理。
> * 性能对比：AFNetworking请求优于ASIHttpRequest；

#### 46.XML数据解析方式各有什么不同，JSON解析有哪些框架？

> * XML数据解析的两种解析方式：DOM解析和SAX解析；
> * DOM解析必须完成DOM树的构造，在处理规模较大的XML文档时就很耗内存，占用资源较多，读入整个XML文档并构建一个驻留内存的树结构（节点树），通过遍历树结构可以检索任意XML节点，读取它的属性和值，通常情况下，可以借助XPath查询XML节点；
> * SAX与DOM不同，它是事件驱动模型，解析XML文档时每遇到一个开始或者结束标签、属性或者一条指令时，程序就产生一个事件进行相应的处理，一边读取XML文档一边处理，不必等整个文档加载完才采取措施，当在读取解析过程中遇到需要处理的对象，会发出通知进行处理。因此，SAX相对于DOM来说更适合操作大的XML文档。
> * -JSON解析：性能比较好的主要是第三方的JSONKIT和iOS自带的JSON解析类，其中自带的JSON解析性能最高，但只能用于iOS5之后。

#### 47.如何进行真机调试(现在不用开发者账号也可以真机调试)

> * 1.首先需要用钥匙串创建一个钥匙（key）；
> * 2.将钥匙串上传到官网，获取iOS Development证书；
> * 3.创建App ID即我们应用程序中的Boundle ID；
> * 4.添加Device ID即UDID；
> * 5.通过勾选前面所创建的证书：App ID、Device ID；
> * 6.生成mobileprovision文件；
> * 7.先决条件：申请开发者账号 99美刀

#### 48.APP发布的上架流程

> * 1.登录应用发布网站添加应用信息；
> * 2.下载安装发布证书；
> * 3.选择发布证书，使用Archive编译发布包，用Xcode将代码（发布包）上传到服务器；
> * 4.等待审核通过;
> * 5.生成IPA：菜单栏->Product->Archive.

#### 49.SVN的使用

> * SVN=版本控制+备份服务器，可以把SVN当成备份服务器，并且可以帮助你记住每次上服务器的档案内容，并自动赋予每次变更的版本；
> * SVN的版本控制：所有上传版本都会帮您记录下来，也有版本分支及合并等功能。SVN可以让不同的开发者存取同样的档案，并且利用SVN Server作为档案同步的机制，即您有档案更新时，无需将档案寄送给您的开发成员。SVN的存放档案方式是采用差异备份的方式，即会备份到不同的地方，节省硬盘空间，也可以对非文字文件进行差异备份。
> * SVN的重要性：备份工作档案的重要性、版本控管的重要性、伙伴间的数据同步的重要性、备份不同版本是很耗费硬盘空间的；
> * 防止冲突：
> * 1.防止代码冲突：不要多人同时修改同一文件，例如：A、B都修改同一个文件，先让A修改，然后提交到服务器，然后B更新下来，再进行修改；
> * 2.服务器上的项目文件Xcodeproj，仅让一个人管理提交，其他人只更新，防止文件发生冲突。

#### 50.如何进行网络消息推送

> * 一种是Apple自己提供的通知服务（APNS服务器）、一种是用第三方推送机制。
> * 首先应用发送通知，系统弹出提示框询问用户是否允许，当用户允许后向苹果服务器(APNS)请求deviceToken，并由苹果服务器发送给自己的应用，自己的应用将DeviceToken发送自己的服务器，自己服务器想要发送网络推送时将deviceToken以及想要推送的信息发送给苹果服务器，苹果服务器将信息发送给应用。
> * 推送信息内容，总容量不超过256个字节；
> * iOS SDK本身提供的APNS服务器推送，它可以直接推送给目标用户并根据您的方式弹出提示。
> * 优点：不论应用是否开启，都会发送到手机端；
> * 缺点：消息推送机制是苹果服务端控制，个别时候可能会有延迟，因为苹果服务器也有队列来处理所有的消息请求；
> * 第三方推送机制，普遍使用Socket机制来实现，几乎可以达到即时的发送到目标用户手机端，适用于即时通讯类应用。
> * 优点：实时的，取决于心跳包的节奏；
> * 缺点：iOS系统的限制，应用不能长时间的后台运行，所以应用关闭的情况下这种推送机制不可用。

#### 51.网络七层协议

> * **应用层：**
> 1.用户接口、应用程序；
> 2.Application典型设备：网关；
> 3.典型协议、标准和应用：TELNET、FTP、HTTP
> * **表示层：**
> 1.数据表示、压缩和加密presentation
> 2.典型设备：网关
> 3.典型协议、标准和应用：ASCLL、PICT、TIFF、JPEG|MPEG
> 4.表示层相当于一个东西的表示，表示的一些协议，比如图片、声音和视频MPEG。
> * **会话层：**
> 1.会话的建立和结束；
> 2.典型设备：网关；
> 3.典型协议、标准和应用：RPC、SQL、NFS、X WINDOWS、ASP
> * **传输层：**
> 1.主要功能：端到端控制Transport；
> 2.典型设备：网关；
> 3.典型协议、标准和应用：TCP、UDP、SPX
> * **网络层：**
> 1.主要功能：路由、寻址Network；
> 2.典型设备：路由器；
> 3.典型协议、标准和应用：IP、IPX、APPLETALK、ICMP；
> * **数据链路层：**
> 1.主要功能：保证无差错的疏忽链路的data link；
> 2.典型设备：交换机、网桥、网卡；
> 3.典型协议、标准和应用：802.2、802.3ATM、HDLC、FRAME RELAY；
> * **物理层：**
> 1.主要功能：传输比特流Physical；
> 2.典型设备：集线器、中继器
> 3.典型协议、标准和应用：V.35、EIA/TIA-232.

#### 52.对NSUserDefaults的理解

> * NSUserDefaults：系统提供的一种存储数据的方式，主要用于保存少量的数据，默认存储到library下的Preferences文件夹。

#### 53.SDWebImage原理

> 调用类别的方法：

> * 从内存中（字典）找图片（当这个图片在本次程序加载过），找到直接使用；
> * 从沙盒中找，找到直接使用，缓存到内存。
> * 从网络上获取，使用，缓存到内存，缓存到沙盒。

#### 54.OC中是否有二维数组，如何实现二维数组

> * OC中没有二维数组，可通过嵌套数组实现二维数组。

#### 55.LayoutSubViews在什么时候被调用？

> * 当View本身的frame改变时，会调用这个方法。

#### 56.深拷贝和浅拷贝

> * 如果对象有个指针型成员变量指向内存中的某个资源，那么如何复制这个对象呢？你会只是复制指针的值传给副本的新对象吗？指针只是存储内存中资源地址的占位符。在复制操作中，如果只是将指针复制给新对象，那么底层的资源实际上仍然由两个实例在共享。
> 
![](/uploads/images/iOS面试题汇总所用图片/浅拷贝.png)

> * 浅复制：两个实例的指针仍指向内存中的同一资源，只复制指针值而不是实际资源；
> * 深复制：不仅复制指针值，还复制指向指针所指向的资源。如下图：

![](/uploads/images/iOS面试题汇总所用图片/浅拷贝.png)

#### 57.对沙盒的理解

> * 每个iOS应用都被限制在“沙盒”中，沙盒相当于一个加了仅主人可见权限的文件夹，即使在应用程序安装过程中，系统为每个单独的应用程序生成它的主目录和一些关键的子目录。苹果对沙盒有几条限制:
> 1. 应用程序在自己的沙盒中运作，但是不能访问任何其他应用程序的沙盒；
> 2. 应用之间不能共享数据，沙盒里的文件不能被复制到其他
> 应用程序的文件夹中，也不能把其他应用文件夹复制到沙盒中；
> 3. 苹果禁止任何读写沙盒以外的文件，禁止应用程序将内容写到沙盒以外的文件夹中；
> 4. 沙盒目录里有三个文件夹：
> Documents——存储应用程序的数据文件，存储用户数据或其他定期备份的信息；
> Library下有两个文件夹：Caches存储应用程序再次启动所需的信息，Preferences包含应用程序的偏好设置文件，不可在这更改偏好设置；
> temp存放临时文件即应用程序再次启动不需要的文件。
> * 获取沙盒根目录的方法，有几种方法：用NSHomeDirectory获取。
> * 获取Document路径：    NSSearchPathForDirectoriesInDomains(NSDocumentDirectory,NSUserDomainMask,YES).

#### 58.对瀑布流的理解

> * 首先图片的宽度都是一样的，1.将图片等比例压缩，让图片不变形；2.计算图片最低应该摆放的位置，哪一列低就放在哪；3.进行最优排列，在ScrollView的基础上添加两个tableView，然后将之前所计算的scrollView的高度通过tableView展示出来。
> * 如何使用两个TableView产生联动：将两个tableView的滚动事件禁止掉，最外层scrollView滚动时将两个TableView跟着滚动，并且更改contentOffset，这样产生效果滚动的两个tableView。

#### 59.ViewController 的 loadView,、viewDidLoad,、viewDidUnload 分别是在什么时候调用的？

> * viewDidLoad在view从nib文件初始化时调用，loadView在controller的view为nil时调用。
> * 此方法在编程实现view时调用,view控制器默认会注册memory warning notification,当view controller的任何view没有用的时候，viewDidUnload会被调用，在这里实现将retain的view release,如果是retain的IBOutlet view 属性则不要在这里release,IBOutlet会负责release 。

#### 60.关键字volatile有什么含意?并给出三个不同的例子：

> * 一个定义为volatile的变量是说这变量可能会被意想不到地改变，这样，编译器就不会去假设这个变量的值了。精确地说就是，优化器在用到这个变量时必须每次都小心地重新读取这个变量的值，而不是使用保存在寄存器里的备份。下面是volatile变量的几个例子：

> • 并行设备的硬件寄存器（如：状态寄存器）；
> 
> •一个中断服务子程序中会访问到的非自动变量(Non-automatic variables)；
> 
> • 多线程应用中被几个任务共享的变量。

#### 61.@synthesize、@dynamic的理解

> * @synthesize是系统自动生成getter和setter属性声明;@synthesize的意思是，除非开发人员已经做了，否则由编译器生成相应的代码，以满足属性声明；
> * @dynamic是开发者自已提供相应的属性声明,@dynamic意思是由开发人员提供相应的代码：对于只读属性需要提供setter，对于读写属性需要提供 setter 和getter。查阅了一些资料确定@dynamic的意思是告诉编译器,属性的获取与赋值方法由用户自己实现, 不自动生成。

#### 62.frame和bounds有什么不同？

> * frame指的是：该view在父view坐标系统中的位置和大小。（参照点是父亲的坐标系统）
> * bounds指的是：该view在本身坐标系统中的位置和大小。（参照点是本身坐标系统）

#### 63.view的touch事件有哪些？
> 
>     - (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event;
>     - (void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event;
>     - (void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event; 
>     - (void)touchesCancelled:(NSSet *)touches withEvent:(UIEvent *)event;

#### 64.自定义实现UITabbarController的原理

> * 运用字典，点击五个按钮的一个可以从字典里选择一个控制器对象，将其View显示到主控制器视图上。

#### 65.iOS中的响应者链的工作原理
> * 每一个应用有一个响应者链，我们的视图结构是一个N叉树(一个视图可以有多个子视图，一个子视图同一时刻只有一个父视图),而每一个继承UIResponder的对象都可以在这个N叉树中扮演一个节点。
> * 当叶节点成为最高响应者的时候，从这个叶节点开始往其父节点开始追朔出一条链，那么对于这一个叶节点来讲，这一条链就是当前的响应者链。响应者链将系统捕获到的UIEvent与UITouch从叶节点开始层层向下分发，期间可以选择停止分发，也可以选择继续向下分发。
> * 如需了解更多细节，请读[这篇文章](https://www.tuicool.com/articles/6VFn2q)

#### 66.View和View之间传值方式
> * 对象的property属性传值；
> * 方法参数传值；
> * NSUserDefault传值；
> * block块传值。

#### 67.property属性的修饰符的作用
> * getter=getName、setter=setName：设置setter与getter的方法名；
> * readwrite、readonly：设置可供访问级别；
> * assign：方法直接赋值，不进行任何retain操作，为了解决原类型与环循引用问题；
> * retain：其setter方法对参数进行release旧值再retain新值，所有实现都是这个顺序；
> * copy：其setter方法进行copy操作，与retain处理流程一样，先对旧值release，再copy出新的对象，retainCount为1。这是为了减少对上下文的依赖而引入的机制。
> * nonatomic：非原子性访问，不加同步， 多线程并发访问会提高性能。注意，如果不加此属性，则默认是两个访问方法都为原子型事务访问。

#### 68.对于Run Loop的理解

> * RunLoop，是多线程的法宝，即一个线程一次只能执行一个任务，执行完任务后就会退出线程。主线程执行完即时任务时会继续等待接收事件而不退出。非主线程通常来说就是为了执行某一任务的，执行完毕就需要归还资源，因此默认是不运行RunLoop的；
> * 每一个线程都有其对应的RunLoop，只是默认只有主线程的RunLoop是启动的，其它子线程的RunLoop默认是不启动的，若要启动则需要手动启动；
> * 在一个单独的线程中，如果需要在处理完某个任务后不退出，继续等待接收事件，则需要启用RunLoop；
> * NSRunLoop提供了一个添加NSTimer的方法，可以指定Mode，如果要让任何情况下都回调，则需要设置Mode为Common模式；
> * 实质上，对于子线程的runloop默认是不存在的，因为苹果采用了懒加载的方式。如果我们没有手动调用[NSRunLoop currentRunLoop]的话，就不会去查询是否存在当前线程的RunLoop，也就不会去加载，更不会创建。

#### 69.SQLite中常用的SQL语句

> * 创建表:creat table 表名 (字段名 字段数据类型 是否为主键, 字段名 字段数据类型, 字段名 字段数据类型...)；
> * 增: insert into 表名 (字段1, 字段2...) values (值1, 值2...)；
> * 删: delete from 表名 where 字段 = 值；
> * 查：select * from 表名 where 字段=值；
> * 改：update 表名 set 字段=值 where 字段=值；

#### 70.XIB与Storyboards的优缺点
> **优点：**

> * XIB：在编译前就提供了可视化界面，可以直接拖控件，也可以直接给控件添加约束，更直观一些，而且类文件中就少了创建控件的代码，确实简化不少，通常每个XIB对应一个类。
> * Storyboard：在编译前提供了可视化界面，可拖控件，可加约束，在开发时比较直观，而且一个storyboard可以有很多的界面，每个界面对应一个类文件，通过storybard，可以直观地看出整个App的结构。
> 
> **缺点：**

> * XIB：需求变动时，需要修改XIB很大，有时候甚至需要重新添加约束，导致开发周期变长。XIB载入相比纯代码自然要慢一些。对于比较复杂逻辑控制不同状态下显示不同内容时，使用XIB是比较困难的。当多人团队或者多团队开发时，如果XIB文件被发动，极易导致冲突，而且解决冲突相对要困难很多。
> * Storyboard：需求变动时，需要修改storyboard上对应的界面的约束，与XIB一样可能要重新添加约束，或者添加约束会造成大量的冲突，尤其是多团队开发。对于复杂逻辑控制不同显示内容时，比较困难。当多人团队或者多团队开发时，大家会同时修改一个storyboard，导致大量冲突，解决起来相当困难。

#### 71.将字符串“2015-04-10”格式化日期转为NSDate类型

     NSString *timeStr = @"2015-04-10";
 
     NSDateFormatter *formatter = [[NSDateFormatter alloc] init];
 
     formatter.dateFormat = @"yyyy-MM-dd";
 
     formatter.timeZone = [NSTimeZone defaultTimeZone];
 
     NSDate *date = [formatter dateFromString:timeStr];
 
     // 2015-04-09 16:00:00 +0000
 
     NSLog(@"%@", date);

#### 72.队列和多线程的使用原理

**在iOS中队列分为以下几种：**

> * 串行队列：队列中的任务只会顺序执行；
> * dispatch_queue_t q = dispatch_queue_create("...", DISPATCH_QUEUE_SERIAL);
> * 并行队列： 队列中的任务通常会并发执行；
> * dispatch_queue_t q = dispatch_queue_create("......",DISPATCH_QUEUE_CONCURRENT);
> * 全局队列：是系统的，直接拿过来（GET）用就可以；与并行队列类似；
> * dispatch_queue_t q = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
> * 主队列：每一个应用程序对应唯一主队列，直接GET即可；在多线程开发中，使用主队列更新UI；
> * dispatch_queue_t q = dispatch_get_main_queue();
> * 更多细节见下图：

![](/uploads/images/iOS面试题汇总所用图片/队列和多线程汇总.png)

#### 73.内存的使用和优化的注意事项

> * 重用问题：如UITableViewCells、UICollectionViewCells、UITableViewHeaderFooterViews设置正确的reuseIdentifier，充分重用；
> * 尽量把views设置为不透明：当opque为YES的时候，默认是YES的，图层的半透明取决于图片和其本身合成的图层为结果，可提高性能；
> * 不要使用太复杂的XIB/Storyboard：载入时就会将XIB/storyboard需要的所有资源，包括图片全部载入内存，即使未来很久才会使用。那些相比纯代码写的延迟加载，性能及内存就差了很多；
> * 选择正确的数据结构：学会选择对业务场景最合适的数组结构是写出高效代码的基础。比如，数组: 有序的一组值。使用索引来查询很快，使用值查询很慢，插入/删除很慢。字典: 存储键值对，用键来查找比较快。集合: 无序的一组值，用值来查找很快，插入/删除很快。
> * gzip/zip压缩：当从服务端下载相关附件时，可以通过gzip/zip压缩后再下载，使得内存更小，下载速度也更快。
> * 延迟加载：对于不应该使用的数据，使用延迟加载方式。对于不需要马上显示的视图，使用延迟加载方式。比如，网络请求失败时显示的提示界面，可能一直都不会使用到，因此应该使用延迟加载。
> * 数据缓存：对于cell的行高要缓存起来，使得reload数据时，效率也极高。而对于那些网络数据，不需要每次都请求的，应该缓存起来，可以写入数据库，也可以通过plist文件存储。
> * 处理内存警告：一般在基类统一处理内存警告，将相关不用资源立即释放掉
> * 重用大开销对象：一些objects的初始化很慢，比如NSDateFormatter和NSCalendar，但又不可避免地需要使用它们。通常是作为属性存储起来，防止反复创建。
> * 避免反复处理数据：许多应用需要从服务器加载功能所需的常为JSON或者XML格式的数据。在服务器端和客户端使用相同的数据结构很重要;
> * 使用Autorelease Pool：在某些循环创建临时变量处理数据时，自动释放池以保证能及时释放内存;
> * 正确选择图片加载方式。

#### 74.UIViewController的完整生命周期

> * -[ViewController initWithNibName:bundle:]；
> * -[ViewController init]；
> * -[ViewController loadView]；
> * -[ViewController viewDidLoad]；
> * -[ViewController viewWillAppear:]；
> * -[ViewController viewWillLayoutSubviews:]；
> * -[ViewController viewDidLayoutSubviews:]；
> * -[ViewController viewDidAppear:]；
> * -[ViewController viewWillDisappear:]；
> * -[ViewController viewDidDisappear:]；
> * -[ViewController viewWillUnload:]；
> * -[ViewController viewDidUnload:]；

#### 75.UIImageView添加圆角
> * 最直接的方法就是使用如下属性设置：
  
      imgView.layer.cornerRadius = 10;
 
		// 这一行代码是很消耗性能的
 
      imgView.clipsToBounds = YES;

> *这是离屏渲染（off-screen-rendering），消耗性能的*
>  
> * 给UIImage添加生成圆角图片的扩展API：这是on-screen-rendering
 
       -(UIImage *)imageWithCornerRadius:(CGFloat)radius {
       CGRect rect = (CGRect){0.f, 0.f, self.size};
 
       UIGraphicsBeginImageContextWithOptions(self.size,NO,UIScreen.mainScreen.scale);
       CGContextAddPath(UIGraphicsGetCurrentContext(),
  
       [UIBezierPath bezierPathWithRoundedRect:rect cornerRadius:radius].CGPath);
       CGContextClip(UIGraphicsGetCurrentContext());
  
       [self drawInRect:rect];
  
       UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
  
       UIGraphicsEndImageContext();
  
       return image;
  
       }

#### 76.了解iOS开发者账号类型

![](/uploads/images/iOS面试题汇总所用图片/iOS开发者账号类型.png)

> * “个人”开发者可以申请升级“公司”，可以通过拨打苹果公司客服电话（400 6701 855）来咨询和办理。
> * 公司账号允许多个开发者进行协作开发，比个人多一些帐号管理的设置，可设置多个Apple ID，分4种管理级别的权限。申请公司账号需要填写公司的邓白氏编码（DUNS Number）。
> * 需要注意的是，企业账号开发的应用不能上线App Store，适合那些不希望公开发布应用的企业。同样，申请时也需要公司的邓白氏编码（DUNS Number）。

#### 77.Property属性

> 1.原子性：nonatomic 、atomic
> 
> 2.读写： readwrite 、readonly
> 
> 3.方法名：getter=<name>、setter=<name>
> 
> 4.内存：strong、retain、copy、weak、assign 、unsafe_unretained
> 
> **原子性和读写特性：**
> 
> * atomic：原子性，只有一个线程可以同时访问实例。atomic 是线程安全的，至少在当前的读取器是安全的。虽然它是一个默认属性，但是由于其使用同步锁开销较大，会损耗性能。
> * nonatomic：非原子性的，可以被多个线程访问。效率要比atomic 高，但是不能保证其在多线程状态下的安全性，在单线程和明确只有一个线程访问的情况下被广泛使用。
> * readwrite（默认值）：表示其同时拥有getter 和 setter 方法；
> * readonly：只读操作，其只有getter 方法，没有setter法
> * 注：如果某个实例只允许被外部读取，而不能写入操作，同时在类实现文件当中可以写入的话，可以在头文件中声明属性为只读的，在实现文件中设置其为可读写的属性，写法如下：
 
         //头文件中声明为：
  
         @property(nonatomic,readonly,copy) NSString *stringA;
  
         //实现文件中声明为:
  
         @property(nonatomic,readwrite,copy) NSString *stringA;
 
 **getter=<name>的样式：**
 
         @property (nonatomic, getter=isOn) BOOL on;
 
 **弱引用**
  
> * assign：用于值类型(如int，float等)
> * weak： 用于修饰引用类型
> * unsafe_unretained：只修饰引用类型
	 
> **区别**：

> * 三者修饰效果相同，即都不会更改所赋新值的引用计数，也不改变旧值的引用计数
> * assign一般只修饰值类型，虽然也可以修饰引用类型，但是修饰的对象释放后，指针不会自动被置空，此时向对象发消息会崩溃。
> * weak 不会产生野指针问题。因为weak修饰的对象释放后（引用计数器值为0），指针会自动被置nil，之后再向该对象发消息也不会崩溃。 weak是安全的。
> * unsafe_unretained与assign的区别在于，其只修饰引用类型。

>**强引用**
>
> * strong：用于引用类型，强引用。
> * retain ：用于引用类型，强引用。
> * copy：修饰属性会在内存里拷贝对象。
> 
> **区别：**

> * 三者都用于修饰引用类型。
> * strong用于ARC，retain用于MRC。
> * copy分为浅层复制和深复制两种，NSString、NSArray、NSDictionary等不可变类型都为浅层复制，即其引用计数会+1，而不会创建新的内存

#### 78.了解沙盒的目录结构

> iOS应用沙盒即文件系统目录，与其他应用的文件系统隔离

![](/uploads/images/iOS面试题汇总所用图片/了解沙盒的目录结构.png)

> * Documents：保存应用运行时生成的需要持久化的数据，如游戏进度、涂鸦软件的绘图等信息， 该目录会被iTunes同步备份。
> * Library/Caches：保存应用运行时生成的需要持久化的数据，iTunes同步设备时不会备份该目录。一般存储体积大、不需要备份的非重要数据，如缓存图片或者离线数据(地图等)。 
> * Library/Preference：保存应用的所有偏好设置，iOS的Settings(设置)应用会在该目录中查找应用的设置信息。iTunes同步设备时会备份该目录。
> * tmp：保存应用运行时所需的临时数据，使用完毕后再将相应的文件从该目录删除。应用没有运行时，系统也可能会清除该目录下的文件。iTunes同步设备时不会备份该目录。重启手机、系统磁盘不足时都会被清理

#### 79.理解内存中的区域划分

> * 栈区(stack):由系统自动分配和释放，存放局部变量的值，容量小速度快，有序
> * 堆：一般由程序员分配和释放，如果不释放，则出现内存泄露。程序会回收您的内存，特点：容量大，速度慢，无序
> * 静态存储区：全局变量（外部变量）和静态变量都存放在静态区域。当程序结束使，系统回收
> * 常量区：存放常量的内存区域，程序结束时，系统回收
> * 代码区：存放二进制代码的区域

#### 80.理解iOS的远程推送

> **简易推送流程：**

![](/uploads/images/iOS面试题汇总所用图片/iOS推送原理.png)

> * (1) 应用程序安装后提示用户是否需要接收推送，用户确认后注册消息推送。
> * (2)App接收到从APNS Server获取的令牌信息;
> * (3)APP将令牌信息发送到自己的服务器端;
> * (4)当需要向用户推送消息时，自己的服务器将向苹果的推送通知服务器(Apple Push Notification Service,以下简称 APNS)发送通知；
> * (5)APNS 会向装有此APP的iPhone设备发送消息

> **第三方推送的原理(以个推为例)：**

![](/uploads/images/iOS面试题汇总所用图片/三方推送原理.png)

#### 81.理解Block的使用

![](/uploads/images/iOS面试题汇总所用图片/Block的声明.png)

> --------Block为什么要用copy？--------
>  
> a、block在创建的时候默认分配的内存是在栈上，而不是在堆上。这样的话其本身的作用域是属于创建时候
> 的作用域，一旦在创建的作用域之外调用就会导致程序的崩溃。所以使用了copy将其拷贝到堆内存上。
> b、block创建在栈上，而block的代码中可能会用到本地的一些变量，只有将其拷贝到堆上，才能用这些变量
> 
> --------Block为什么不用retain？--------
> 
> retain这是增加了一次计数，block的内存还是在栈上，并没有存在堆上，存在栈上的block可能随时被系统回收。
> 
> --------为什么进入block中的对象引用计数需要自动加1？--------
>  
> Block执行的是回调，因此block并不知道其中的对象obj创建后会在什么时候被释放，为了不在block使用obj之前，对象已经被释放，block就retain了obj一次
> 
> --------block和函数的关系？--------
> 
> Block的使用很像函数指针，不过与函数最大的不同是Block可以访问函数以外、词法作用域以内的外部变量的值。
> 换句话说，Block不仅 实现函数的功能，还能携带函数的执行环境。
> 
> --------对于block的理解？--------
> 
> block实际上是: 指向结构体的指针
> 编译器会将block的内部代码生成对应的函数
> 
> --------对于基本数据类型，进入到block中会被当做常量处理？--------
 
        //如果需要在block中对num进行修改，需要加上关键字__block
  
        //(我们也可以用static关键字进行修饰)
  
        int  num1 = 10;
    
        void(^block1)() = ^{
    
         NSLog(@"num1 is %d",num1);
         
        };
    
        num1 = 20;
    
        block1(); //输出10
         
        //改进:使用block,使进入到block块中的变量不被当做常量来使用
    
        __blockint  num2 = 10;
    
        void(^block2)() = ^{
    
       NSLog(@"num2 is %d",num2);
       
        };
    
        num2 = 20;
    
        block2(); //输出20
   
 
> --------Block中self的循环引用？--------
> 
> block默认创建在栈上，所以对要对其进行执行copy操作，将其拷贝到堆区，便于更好的操作对象。但是执行了copy操作之后，block中使用self，此对象会被retain一次（注意：block在堆区上时才会起到retain作用），会造成循环引用。
> 解决方法：
> 在MRC下，使用__block修饰
> 在ARC下，使用__unsafe_unretained\weak修饰

#### 82.理解循环引用出现的三种情况：

> * NSTimer
>  NSTimer对象timer作为一个对象A的属性，本意在A的dealloc中释放timer,但是timer没有停止就不会触发dealloc，然后就互相等待，造成循环引用。解决方法是显式的调用timer的关闭方法[timer invaluate],再释放A对象
>  
> * Block
> block代码块没有配合weak使用
>  
> * Delegate
> 声明delegate时请用assign(MRC)或者weak(ARC)。

> **循环引用举例：**

>     NSMutableArray *firstArray = [NSMutableArray array]; 
> 
>     NSMutableArray *secondArray = [NSMutableArray array];
> 
>     [firstArray addObject:secondArray]; 
> 
>     [secondArray addObject:firstArray];
> 

> **检测循环引用**

> * Xcode -> Product -> Pofile -> Leaks

#### 83.理解指针的使用

> **用变量a给出下面的定义[原文链接](https://blog.csdn.net/ropenyuan/article/details/6678080)**
> 
> a) 一个整型数
> 
> b) 一个指向整型数的指针
> 
> c) 一个指向指针的的指针，它指向的指针是指向一个整型数
> 
> d) 一个有10个整型数的数组
> 
> e) 一个有10个指针的数组，该指针是指向一个整型数的
> 
> f) 一个指向有10个整型数数组的指针
> 
> g) 一个指向函数的指针，该函数有一个整型参数并返回一个整型数
> 
> h) 一个有10个指针的数组，该指针指向一个函数，该函数有一个整型参数并返回一个整型数

#### 84.理解iOS中的多线程

**1.概念理解:**

> 多线程是针对于单核的CPU来设计的，目的是为了让CPU快速在多个线程之间进行调度。
> 
> * 多线程的优缺点
>  优点：提高程序的执行效率
>  缺点：开启线程需要一定的内存空间
>  
> * 同步和异步：决定了可不可以开启新的线程
>  同步：在当前线程中执行任务，不具备开启新线程的能力
>  异步：在新的线程中执行任务，具备开启新线程的能力
>  
> * 并行与串行：决定了任务的执行方式
>  并行：多个任务并发（同时）执行。类型迅雷，多个任务同时开启下载
>  串行：一个任务执行完毕后，再执行下一个任务。类似浏览器的一个接一个下载

![](/uploads/images/iOS面试题汇总所用图片/同步异步和串行并行.png)

> *iOS应用程序中都是一个主线程，也称为UI线程
> 那么主线程的作用就是用来更新UI，显示或者刷新界面
> 注意：不能将耗时的任务放在主线程上，否则会出现卡顿的现象。*

> **2.iOS的三种多线程编程技术**
> 
> * NSThread
> 直接操作线程对象，但需要手动管理生命周期，而且经常使用这种方式来查看当前线程
> * GCD（Grand Central Dispatch）
> 底层使用的是C语言，灵活方便，可以根据系统负荷来增减线程，性能效率更好
> * Cocoa NSOperation
> NSOperation对GCD的封装，使用起来更好理解，将任务封装为NSOpertaion,添加到NSOPerationQueue对象中
> 子类化NSOpertaion的设计，更具有面向对象（封装，复用）的特性。更加适合在复杂项目中使用
> 
> **3.进程与线程**
> 
> Progress和Thread,进程和线程是操作系统里的基本概念
> 
> * 线程与进程的区别:
> 线程是资源分配的最小单位，也是处理器调度的基本单位，但是进程不是
> 进程是资源拥有的单位，同一个进程内的线程共享进程里的资源
> * 多进程，允许多个任务同时运行
> * 多线程，允许单个任务分为不同的部分运行

#### 85.了解音频播放相关知识

> *音频的播放从形式上分为音频播放和音乐播放。
> 音频播放：通常时间较短，不需要进度控制，和循环控制。使用AudioToolbox.framework。
> 音乐播放：通常时间较长，需要进行精准控制。使用AVFoundation.framework。*
> 
> **音频播放**
> AudioToolbox.framework是基于C语言的框架。
> 
> * 原理：将短音频注册的到系统声音服务(System Sound Service)中。System Sound Service是一种简单、底层的声音播放服务。
> * 1.音频播放时间不能超过30秒。
> * 2.数据必须是PCM或者IMA4格式。
> * 3.音频格式必须打包成.caf、.aif、wav中的一种。（这是官方说法，实际发现一些.mp3也可以)。
> 
> **音乐播放**
> 
> * 1.适合播放较大的音频。
> * 2.可以对音频进行精准的播放控制
> * 3.使用AVFoundataion.framework中的AVAudioPlayer来实现。
>  
> **使用：**
> 
> * 1.初始化AVAudioPlayer对象，通常是指定本地文件路径
> * 2.设置播放器属性，例如重复次数，音量大小等。
> * 3.调用play方法播放。
> 
> **注意：**
> * AVAudioPlayer一次只能播放一个音频文件，所有的上一曲和下一曲都是通过创建多个AVAudioPlayer来实现的。

#### 86.理解NSOperationQueue

> * NSOperationQueue是存放NSOPeration的集合类，可以参考JAVA中的线程和线程池的概念。
> * 虽说是queue，但并不是队列的意思，并不遵守先进先出。
> * 所以我们可以理解为Pool ，即线程池。

#### 87.理解OC是动态运行时语言

> * OC将数据、对象类型的确定从编译阶段推迟到了运行时。实现这一操作的基础是面向对象语言的多态特性。
> * 这里面有有两个关键字：运行时和多态
> * 运行时：运行时机制使我们知道运行的时候才确定一个对象的类型、以及调用该类别对象指定的方法。
> * 多态：不同的对象以自己的方式来响应相同的消息。子类的指针可以赋值给父类。

#### 88.GCD的queue、main queue中执行的代码一定是在main thread么？

> *“queue中所执行的代码不一定在main thread中。如果queue是在主线程中创建的，那么所执行的代码就是在主线程中执行。如果是在子线程中创建的，那么就不会在main thread中执行。“*
> 
> 上述说法并不完全正确，queue中所执行的代码不一定在主线程是对的，但是队列Queue中执行的任务是在否在主线程与创建队列所在的线程并无关系。
> 
> ##### **对于这个问题，首先总结几个知识点：**
> 
> **1.iOS中获取队列的三种方式：**
> 
> * 主线程队列：
> 主线程队列为串行队列，和主线程绑定。同普通串行队列一样，队列中任务一次只能执行一个，但是队列中所有任务都在主线程中执行(经过测试，即使是异步添加的任务，也没有创建新的线程)。
> 
> * 全局队列：
> 系统全局队列为并发队列，根据不同的优先级(HIGH、DEFAULT、LOW、BACKGROUND)有四个。
> 
> * 自定义队列：
> 系统提供方法，可以自定义创建串行和并行队列。
> 
> **2.同步与异步，串行与并行**
> 
> *同步与异步：决定可不可以开启新的线程*
> 
> * 同步：在当前线程立即执行添加的任务，不具备开启新线程的能力。
> * 异步：在新的线程中执行任务，具备开启新线程的能力
> 
> *并行与串行：决定了任务的执行方式*
> 
> * 并行：可以多个任务并发（同时）执行。类似迅雷，多个任务同时开启下载
> * 串行：一个任务执行完毕后，再执行下一个任务。类似浏览器的一个接一个下载

![](/uploads/images/iOS面试题汇总所用图片/同步异步和串行并行.png)

> 对于这个问题，我使用代码测试了使用的情况如下：

![](/uploads/images/iOS面试题汇总所用图片/同步异步和串行并行组合情况.png)

> *总结：队列Queue中执行的任务是在否在主线程与创建队列所在的线程无关。*

> * 判断一个任务是不是在主线程，我们可以首先判断同步还是异步，因为异步才具有开启新线程的能力。然而我们还需要注意两点：
> * 1.主线程所在队列为串行队列，添加同步会导致死锁。
> * 2.并非所有的异步任务都不在主线程中，主线程队列中添加异步任务，并不开启新的线程。

#### 89.常见的Http状态码有哪些？

> * 302是请求重定向。
> * 500及以上是服务器错误，如503表示服务器找不到、3840表示服务器返回无效JSON。
> * 400及以上是请求链接错误或者找不到服务器，如常见的404。
> * 200及以上是正确，如常见的是200表示请求正常。

> 更多参考： [Http状态码详细说明](http://tool.oschina.net/commons?type=5)

#### 90.区分UDID与UUID

> * **UDID**（Unique Device Identifier）：用户设备唯一编码
> 
> UDID是一串由40位16进制数组成的字符串，用以标识唯一的设备。苹果从iOS5开始就移除了通过代码访问UDID的权限，所以我们无法从代码中获取用户设备的UDID。如果我们想查看自己设备的UDID，可以通过iTunes来查看。通常我们在增加Provisioning Profile文件时会用到。
> 
> * **UUID**（Universally Unique IDentifier）：通用唯一识别符
> 
> UUID是一个32位的十六进制序列，使用小横线来连接：8-4-4-4-12 。它是一种应用加设备绑定产生的标识符，可以通过代码获得。当在设备上安装来自同一个供应商的不同App时，此值保持不变。如果你删除了来自某个供应商的所有app，再重新安装时，此值会改变。
> 
> **获取UUID的方法：**
> NSString *uuidString = [[[UIDevice currentDevice] identifierForVendor] UUIDString];

#### 91.区分nil 、Nil、NULL、NSNUll

> * nil
>  
> nil一般是指把一个对象置空，既完全是一个空对象，完全从内存中释放。
> 
> * Nil
>  
> Nil和nil基本没有任何区别，也可以说只要是可以使用nil的地方都可以使用Nil，反之亦然。但是作为程序猿，我们应该更加严谨一些。nil和Nil的区别在于，nil表示置空一个对象，而Nil表示置空一个类。
> 
> * NULL
>  
> 大家都知道oc 是基于c的，并且oc是完全兼容c的，NULL源于c，表示一个空指针.
> 即：int *p = NULL
> 
> * NSNull
>  
> NSNull很有意思，大家一般都会觉得，NSNull也是空，但是看着这货又是“NS”开头的很像一个对象，实质上NSNull的确是一个对象，它继承于NSObject。那它和nil的区别在哪里呢？nil是把一对象完全释放，就是完全从内存中释放。但是当我想把一个对象置空但是又想要一个容器的时候，我们就可以使用NSNull。比如一瓶矿泉水，我们不想要里面的水，但是我们想保留瓶子一样。

#### 92.区分MRC和ARC内存管理

> * iOS5.0以后出现的ARC，即自动引用计数(Automatic Refrence Counting)，减少了代码，省去了对象释放的麻烦。
> 
> * ARC项目：加入MRC：
> 
>  target ->build phrases ->compbile sources ，点击mrc的文件将其设置为 -fno-objc-arc
> 
> * MRC项目：加入ARC：
> 
>  target ->build phrases ->compbile sources ，点击arc的文件将其设置为 -fobjc-arc
>  
> * iOS通过引用计数来记录对象的引用，每次runloop完成一次循环的时候，都会检查对象的retainCount，如果说对象的retainCount为0，说明该对象没有地方需要引用了，就可以释放掉了。

#### 93. 区分黑盒测试与白盒测试

> * 黑盒：已知产品所应具有的功能，通过测试来检测每个功能是否都能正常使用， “黑盒”法着眼于程序外部结构、不考虑内部逻辑结构、针对软件界面和软件功能进行测试。“黑盒”法是穷举输入测试，只有把所有可能的输入都作为测试情况使用，才能以这种方法查出程序中所有的错误。
>  
> * 白盒：全面了解程序内部逻辑结构、对所有逻辑路径进行测试。“白盒”法是穷举路径测试。在使用这一方案时，测试者必须检查程序的内部结构，从检查程序的逻辑着手，得出测试数据

#### 94.区分深拷贝与浅拷贝

> * 浅拷贝：指针拷贝，不增加新的内存。只是新增加一个指针指向原来的内存区域。
> * 深拷贝：内容拷贝，同时拷贝指针和指针指向的内存。新增指针指向新增的内存。
> * 拷贝条件：
>  iOS中并非所有的对象都支持copy和mutableCopy，只有遵循了NSCopy协议或者NSMutableCoy协议的类才行。如果遵循着两个协议就必须分别实现copyWithZone和mutableCopyZone方法
> * 拷贝原则：
> 
>  1.非容器类：像NSString、NSNumber这样的不能包含其他对象的系统类
>  不可变对象调用copy是浅拷贝；而调用muablecopy是深拷贝并得到可变对象
>  可变对象调用copy和mutablecopy都是深拷贝， 区别在于copy返回不可变对象，mutablecopy返回可变对象
>  
>  2.容器类：像NSArray、NSMutableArray等系统类
>  不可变对象调用copy是浅拷贝，而调用muablecopy是深拷贝并得到可变对象。
>  可变对象调用copy和mutablecopy都是深拷贝，区别在于copy返回不可变对象，mutablecopy返回可变对象
>  
>  容器类与非容器类的拷贝原则相似，但需要注意的是：所有的容器类的拷贝，拷贝后新容器里的元素始终是浅拷贝，其指针都指向原来对象。
>  
>  3.自定义类：比如我们自定义一个Student类(实现拷贝协议)
>  拷贝协议的具体实现不同，拷贝效果也就不同。在实现的拷贝协议方法中直接返回对象的self就相当于浅拷贝了，但是是如果返回新创建对象就是深拷贝了。

关于拷贝的可参考链接：[参考链接1](https://blog.csdn.net/chenyufeng1991/article/details/51771728#) 、[参考链接2](https://www.jianshu.com/p/f01d490401f9https://www.jianshu.com/p/f01d490401f9)

#### 95.区别Strong和weak

> * 在网上找个比较形象的列子来方便理解：
> 
>  想象我们研究的对象是一条狗，狗想要跑掉（被释放）。
> 
> * 1.Strong
>  strong型指针就像是栓住的狗。只要你用牵绳挂住狗，狗就不会跑掉。如果有5个人牵着一条狗（5个strong型指针指向1个象），除非5个牵绳都脱落 ，否着狗是不会跑掉的。
> * 2.Weak
>  weak型指针就像是一个小孩指着狗喊到：“看！一只狗在那” 只要狗一直被栓着，小孩就能看到狗，（weak指针）会一直指向它。只要狗的牵绳脱落，狗就会跑掉，不管有多少小孩在看着它。
>  　　
> * 总结：只要最后一个strong型指针不再指向对象，那么对象就会被释放，即使还有weak型指针指向它。一旦最后一个strong型指针离去 ，这个对象将被释放，所有剩余的weak型指针都将被清除。

#### 96.区分MVC与MVVM

> * MVC弊端：Controller通常负责Model和View关联，造成View和Model的耦合度高，而且Controller变得庞大复杂。
> * MVVM优点：
> 
>  1.低耦合。View可以独立于Model变化和修改，一个ViewModel可以绑定到不同的View上，当View变化的时候Model可以不变，当Model变化的时候View也可以不变。
> 
>  2.可重用性。可以把一些视图的逻辑放在ViewModel里面，让很多View重用这段视图逻辑。
> 
>  3.独立开发。开发人员可以专注与业务逻辑和数据的开发(ViewModel)。设计人员可以专注于界面(View)的设计。
> 
>  4.可测试性。可以针对ViewModel来对界面(View)进行测试

#### 97.区分类目与扩展

> * 类目:category 为已知的类增加新的方法
> 
> 1.类目中扩展的方法会被子类继承
> 
> 2.增加原有类的方法，而且是可以增加多个类目将大的功能划分为小功能。
> 
> 3.类目中的方法会比原有类中的方法具有更高优先级。所以不能和原有类方法重名否则覆盖。
> 
> 4.类目只能添加方法，不能添加变量
> 
> 
> * 扩展：即延展，一般是在一个类的实现文件中。给当前类添加私有变量和私有方法。添加的方法是必须实现的

#### 98.区分#include、#import和@class

> * "#include"
> 
> 一般来说，导入objective-C的头文件时用#import，导入c/c++头文件用#include。include相当于拷贝文件中的声明内容，多次使用就会报重复定义的错误。如： class A，class B都引用了class C，而class D中又同时引用class A与class B，就会报重复引用的错误。
> 
> * "#import"
> 
> 不会产生重复定义的错误，因为它会做一次判断，如果已经导入就不再导入了
> 
> * @class
> 
> @class仅仅是类的声明，告诉编译器有这么个类，具体这个类怎么定义一无所知。
> @class在编译的时候，速度更快，解决引用循环依赖死锁的问题(类的扩展，代理设计模式）
> @class还可以解决循环依赖的问题，例如A.h导入了B.h，而B.h导入了A.h，每一个头文件的编译都要让对象先编译成功才行。这样的话，在头文件中一般使用@class来声明这个名称是类的名称。 而在实现类里面，因为会用到这个引用类的内部的实体变量和方法，所以需要使用#import来包含这个被引用类的头文件。
> 
> * 注：＃import<> 跟 #import””区别：＃import<> 包含iOS框架类库里的类，#import""包含项目里自定义的类。

#### 99.区分TCP和UDP

> * TCP：面向连接、传输可靠（保证数据正确性，保证数据顺序传输）、用于传输大量数据(流模式)、速度慢，建立连接需要开销较多(时间，系统资源)。
>  
> * UDP：面向非连接、传输不可靠、用于传输少量数据(数据包模式)、速度快，传输的是报文。

#### 100.区分HTTP与Socket

> * HTTP请求：客户端主动发起请求，服务器才能给予响应，一次请求完毕后则断开连接，节省资源。
> * Socket：客户端与服务器端直接使用socket套接字连接，双方保持连接通道，都可以主动发送数据，适合游戏或股票等这种即时性很强的要求。主要使用的类是CFSockdetRef。

#### 101.区分KVC和KVO

> * KVC:值编码，一种使用字符串标识属性，间接访问对象属性的方法。而不是调用存取方法。
> * KVO:观察者模式。通过监听对象的属性来更新UI或者状态

#### 102.区分MD5和Base64两种加密

> * "数据加密的基本过程就是对原来为明文的文件或数据按某种算法进行处理，使其成为不可读的一段代码，通常称为“密文”，使其只能在输入相应的密钥之后才能显示出本来内容，通过这样的途径来达到保护数据不被非法人窃取、阅读的目的。 该过程的逆过程为解密，即将该编码**信息转化为其原来数据的过程。"
>  
> * "加密技术通常分为两大类：“对称式”和“非对称式”。"
> 
>  *--------以上内容摘自百度--------*
> 
> * MD5：严格来说不算加密算法，只能说是摘要算法，而且是一种不可逆的摘要算法。MD5用于生成摘要，并且无法逆破解得到原文。我们常用它验证数据的有效性，比如，在网络请求接口中，通过将所有的参数生成摘要，客户端和服务端采用同样的规则生成摘要，验证数据的有效性。
>  
> * Base64：一种用64个字符来表示任意二进制数据的编码方式，严格来说也不算是一种加密。Base64的操作是可逆的，经过encode加密，可以decode得到原文。Base64常用来解决网络请求中特殊编码问题和轻量型的加密(转化为非明文)。
>  
> * 关于这两种技术是否为加密算法的讨论有很多，我是这样理解的：
>  严格意义上来说，这两种技术都不能算是真正的加密算法。但是把明文转化为不可读的密文，这本就算是一种加密。在日常的开发工作中，我们经常用这两种方式将一些数据转为不可读密文，因此称它们叫做加密算法也不为过。至于是否为加密算法只是一种说法而已，只要理解了其基本原理与用法即可。又或许我们把它们叫作为一种加密方式会好些。

#### 103.区分define定义的宏和const定义的常量

> * define定义宏的指令，程序在预处理阶段将用#define所定义的内容只是进行了替换。因此程序运行时，常量表中并没有用#define所定义的宏，系统并不为它分配内存，而且在编译时不会检查数据类型，出错的概率要大一些。
>  
> * const定义的常量，在程序运行时是存放在常量表中，系统会为它分配内存，而且在编译时会进行类型检查。

#### 104.区分id与instancetype

> * id:万能指针，指向任意类型
> * instancetype：只能作为方法的范围类型，并且返回的类型是当前定义类的类型。

#### 105.区分通知和代理

> * 同：都用于对象之间的通信
> * 异：代理是一对一通信。通知可以一对一，也可以一对多

#### 106.区分methord和selector

> * selector只是一个方法名，而method包含了方法名和方法实现。

#### 107.区分isKindOfClass 与isMemberOfClass

> * isKindOfClass:确定一个对象是否是一个类的成员,或者是派生自该类的成员.
> * isMemberOfClass:确定一个对象是否是当前类的成员.
> * 注:isMemberOfClass不能检测任何的类都是基于NSObject类这一事实，而isKindOfClass可以。

#### 108.区别面向过程和面向对象

> * 面向过程：以事件为编程中心，各功能的实现是按照事件的先后顺序或者因果关系来展开的编程的一种思想
>  
> * 面向对象：以对象为编程的中心，以事件为驱动，各功能是模块化的，彼此之间独立互不影响的一种编程思想。

#### 109.单例的两种写法

> * 单例的目的：通过特殊的构造在不同的地方可以得到同一个对象
> 
> * 方法一：类方法加锁（使用synchronized关键字）
 
         static Person *ps;
  
         +(Person *)sharePerson{
  
         if (ps == nil) {
  
             @synchronized(self){
  
                 //加上锁
  
                  ps = [[Person alloc] init];
  
             }
  
         }
  
         return  ps;
  
         }
 
> * 方法二：通过GCD方法创建
 
         //dispatch_once只执行一次，确保dispatch_once中的代码块在应用程序里面只执行一次，无论是不是多线程。
  
         + (Person *)sharePerson{
          
         static dispatch_once_t onceToken;
      
         dispatch_once(&onceToken, ^{
      
             ps = [[Person alloc] init];
          
         });
      
         return  ps;
      
         }

#### 110.多任务合并的网络请求处理

> * 题目分析：10个请求要全部完成后，才执行某一功能。比如，下载10图片后合成一张大图，就需要异步全部下载完成后，才能合并成大图。
> * 实现思路：通过dispatch_group_t来实现，将每个请求放入到Group中，将合并成大图的操作放在dispatch_group_notify中实现。

>         dispatch_queue_tqueue = 
>  
>         dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
>  
>         dispatch_group_tgroup = dispatch_group_create();
>  
>         dispatch_group_async(group, queue, ^{ /*加载图片1 */ });
>  
>         dispatch_group_async(group, queue, ^{ /*加载图片2 */ });
>  
>         dispatch_group_async(group, queue, ^{ /*加载图片3 */ }); 
>  
>  
>         dispatch_group_notify(group, dispatch_get_main_queue(), ^{
>  
>            // 合并图片
>     
>         });

#### 111.使用NSLog输出一个浮点类型，保留一位小数

> //结果四舍五入，保留一个小数
> 
>     float money = 1.011；
> 
>     NSlog(@“%.1f”，money);

#### 112.内存优化的方案

> * 1.首选使用ARC环境开发，但是也要注意防止循环引用的产生，避免内存泄漏
> * 2.懒加载，延迟创建对象，需要的时候才创建节省内存消耗
> * 3.复用，单元格的服用，避免过多的创建对象
> * 4.选择正确的是数据结构，不必要的时候不使用可变容器
> * 5.单例模式
> * 6.及时删除缓存信息

#### 113.Xcode工具的使用

> * 在开发过程中遇到类似内存泄漏的问题，我们可以通过Xcode中的Instruments来分析解决问题：
> 
>  【Xcode】 > 【Open Developer Too】>【 Instruments】
> 
>  1.Leaks：检测内存泄漏
> 
>  2.zombies ：僵尸对象，指的是对应的内存已经被释放并且不再会使用到，但是你的程序却在某处依然有指向它的引用

#### 114.iOS中的回调机制

> * 1.代理：也叫委托，当某个对象收到多个事件，并要求同一个对象来处理所有事件时。委托机制依赖于某个协议定义的方法来发送消息。
> * 2.通知：当需要多个对象或两个无关对象处理同一个事件时使用。
> * 3.Block：适用于回调只发生一次的简单任务。

#### 115.BAD_ACCESS的错误原因

> * 原因:访问了野指针，比如已经释放的对象的成员变量或者发送消息。
> * 调试：Enaable zombie objects 、设置全局断点

#### 116.解决报错libc++abi.dylib handler threw exception

> * 遇到这种错误，即使用了All Exceptions，也断点到相应的代码了，但是没打印对应的日志。此时我们可以添加如下的代码来打印异常log:
 
         @try{  
           //可能出现错误的代码片段
         }  @catch(NSException *exception) {  
              NSLog(@"exception:%@", exception);  
         }  @finally {     
     
           }

#### 117.堆和栈的区别

> * **1.内存管理范围**
> 
>  只有oc对象需要进行内存管理
> 
>  非oc对象类型比如基本数据类型不需要进行内存管理
> 
> * **2.内存管理本质**
> 
>  因为：Objective-C的对象在内存中是以堆的方式分配空间的,并且堆内存是由你释放的，就是release
> 
>  OC对象存放于堆里面(堆内存要程序员手动回收)
> 
>  非OC对象一般放在栈里面(栈内存会被系统自动回收)
> 
>  堆里面的内存是动态分配的，所以也就需要程序员手动的去添加内存、回收内存
> 
> * **3.内存分配以及管理方式**
 
  **按分配方式分:**
 
>  堆是动态分配和回收内存的，没有静态分配的堆
> 
>  栈有两种分配方式：静态分配和动态分配
> 
>  静态分配是系统编译器完成的，比如局部变量的分配
> 
>  动态分配是有alloc函数进行分配的，但是栈的动态分配和堆是不同的，它的动态分配也由系统编译器进行释放，不需要程序员手动管理
> 
>  **按管理方式分:**
> 
>  对于栈来讲，是由系统编译器自动管理，不需要程序员手动管理
> 
>  对于堆来讲，释放工作由程序员手动管理，不及时回收容易产生内存泄露
> 
>  **堆：**是大家共有的空间，分全局堆和局部堆。全局堆就是所有没有分配的空间，局部堆就是用户分配的空间。堆在操作系统对进程 初始化的时候分配，运行过程中也可以向系统要额外的堆，但是记得用完了要还给操作系统，要不然就是内存泄漏。堆里面一般 放的是静态数据，比如static的数据和字符串常量等，资源加载后一般也放在堆里面。一个进程的所有线程共有这些堆 ，所以对堆的操作要考虑同步和互斥的问题。程序里面编译后的数据段都是堆的一部分。
> 
>  **栈：**是个线程独有的，保存其运行状态和局部自动变量的。栈在线程开始的时候初始化，每个线程的栈互相独立，因此 ，栈是　thread safe的。每个c++对象的数据成员也存在在栈中，每个函数都有自己的栈，栈被用来在函数之间传递参数。操作系统在切换线程的时候会自动的切换栈，就是切换ss/esp寄存器。栈空间不需要在高级语言里面显式的分配 和释放。
> 
>  **一句话总结:**  就是 堆：由程序员分配和释放,如果不释放可能会引起内存泄漏 栈：由编译器自动分配和释放，一般存放参数值，局部变量

#### 118.KVO和KVC

> * KVC，即是指 NSKeyValueCoding(键值编码),提供一种机制来间接访问对象的属性。KVC 就是基于KVO技术来实现的。
> * KVO，提供了一种观察者的机制，通过对某个对象的某个属性添加观察者，当该属性改变，就会调用"observeValueForKeyPath:"方法。

#### 119.OC中创建线程的方法是什么?如果在主线程中执行代码， 方法是什么?如果想延时执行代码、方法是什么?

> * 1、线程创建有三种方法：使用NSThread创建、使用GCD的dispatch、使用子类化的NSOperation,然后将其加入NSOperationQueue;
> * 2、在主线程执行代码，方法是performSelectorOnMainThread:withObject:waitUntilDone:;
> * 3、如果想延时执行代码可以用performSelector:onThread:withObject:waitUntilDone:；

#### 120.指针与数组名的关系?

>      int arrayName[4] = {10, 20, 30, 40};
>  
>      int *p = (int *)(&arrayName + 1);
>  
>      NSLog(@"%d", (&arrayName - 1));

> * 1.(&arrayName + 1)：&arrayName是数组的地址（等价于指向arrayName数组的指针）
> * 2.增加 1 会往后移动16个字节，开始是4个字节的位置，移动后就是16个字节后面的位置（也就是目前位置是20个字节）
> * 3.最后又赋值给，int类型的指针p（int类型占4个字节）
> * 4.所以(p - 1)就是减去4个字节，变成为16个字节的位置，输出的(p - 1)值为40
> * int *p = (int *)(&arrayName + 1);
> * NSLog(@"%d", *(p - 1));//输出结果为 40

#### 121.属性readwrite.readonly,assign,retain,copy,nonatomic 什么作用 ? 在哪种情况下 ?

> * readwrite 是可读可写特性；需要生成getter方法和setter方法时（补充：默认属性，将生成不带额外参数的getter和setter方法（setter方法只有一个参数））
> * readonly 是只读特性 只会生成getter方法 不会生成setter方法 ;不希望属性在类外改变
> * assign 是赋值特性，setter方法将传入参数赋值给实例变量；仅设置变量时；
> * retain 表示持有特性，setter方法将传入参数先保留，再赋值，传入参数的retaincount会+1;
> * copy 表示拷贝特性，setter方法将传入对象复制一份；需要完全一份新的变量时。
> * nonatomic 非原子操作，决定编译器生成的setter getter是否是原子操作，atomic表示多线程安全，一般使用nonatomic

#### 122. IBOutlet 连出来的视图属性为什么可以被设置成weak?

> * 在 storyboard 中添加一个控件引用关系是这样的(以 UIbutton 为例): UIviewController -> UIview -> UIbutton
> * 此时 UIviewController 强引用着 UIview , UIview 强引用着 UIbutton , IBoutlet 连线到控制器的. m 或者. h 中作为视图的属性时用 weak 修饰就可以了, (觉得用 strong 修饰也可以但是没有必要)
> * 添加到子控件也是强引用: UIbutton 就是添加到了 UIviewController 的 view 上

#### 123.预处指令#define声明一个常数， 以表明一年中有多少秒(忽闰问题)。

> * 代码如下：#define SECONDS_PER_YEAR (60 * 60 * 24 * 365)UL

#### 124.重写-个NSString类型的，retain 式声明name属性的 setter和getter 法(MRC)

> * 属性的三大特性:语义特性,原子特性,读写特性.
> * 同时重写setter和getter方法,@synchronized name = _name,关联属性和实例变量
> 
 如： 
 
     -(void)setName:(NSString *)name{
 
     if(_name != name){
 
     [_name retain];
 
     [_name release];
 
     _name = name;
 
     }
 
     }
 
     -(NSString *)name{
 
     return [[_name retain]autorelease]

     }

#### 125.分析json、xml 的区别? json、xml 解析 式的底层是如何让处理的

> * (一)JSON与XML的区别：
> 
> （1）可读性方面：基本相同，XML的可读性比较好；
> 
> （2）可扩展性方面：都具有良好的扩展性；
> 
> （3）编码难度方面：相对而言，JSON的编码比较容易；
> 
> （4）解码难度：JSON的解码难度基本为零，XML需要考虑子节点和父节点；
> 
> （5）数据体积方面：JSON相对于XML来讲，数据体积小，传递的速度比较快；
> 
> （6）数据交互方面：JSON与javascript的交互更加方便，更容易解析处理，更好的数据交互；
> 
> （7）数据描述方面：XML对数据描述性比较好
> 
> （8）传输速度方面：JSON的速度远远快于XML。
> 
> * （二）JSON与XML底层实现原理：
>  
> 　（1）JSON底层原理：遍历字符串中的字符，最终根据格式规定的特殊字符，比如{}、[]、：等进行区分，{}号表示字典，[]号表示数组，：号是字典的键和值的分水岭，最终仍是将JSON转化为字典，只不过字典中的值可能是“字典、数组或者字符串而已”。
> 
> （2）XML底层原理：XML解析常用的解析方法有两种：DOM解析和SAX解析；DOM采用的是树形结构的方式访问XML文档，而SAX采用的是事件模型；DOM解析把XML文档转化为一个包含其内容的树，并可以对树进行遍历，使用DOM解析器的时候需要处理整个XML文档，所以对内存和性能的要求比较高；SAX在解析XML文档的时候可以触发一系列的事件，当发现给定的tag的时候，他可以激活一个回调方法，告诉该方法指定的标签已经找到，SAX对内存的要求通常会比较低，因为他让开发人员自己来决定所要处理的tag，特别是当开发人员只需要处理文档中所包含部分数据时，SAX这种扩展能力得到了更好的体现。

#### 126.对程序性能的优化你有什么建议?

> * 1.使用复用机制
> * 2.尽可能设置 View 为不透明
> * 3.避免臃肿的 XIB 文件
> * 4.不要阻塞主线程
> * 5.图片尺寸匹配 UIImageView
> * 6.选择合适的容器
> * 7.启用 GZIP 数据压缩
> * 8.View 的复用和懒加载机制
> * 9、缓存：
> 
> 服务器的响应信息（response）。
> 
> 图片。
> 
> 计算值。比如：UITableView 的 row heights。
> 
> * 10.关于图形绘制
> * 11.处理 Memory Warnings
> 
> 在 AppDelegate 中实现-[AppDelegate applicationDidReceiveMemoryWarning:] 代理方法。
> 
> 在 UIViewController 中重载 didReceiveMemoryWarning 方法。
> 
> 监听 UIApplicationDidReceiveMemoryWarningNotification 通知。
> 
> * 12.复用高开销的对象
> * 13.减少离屏渲染(设置圆角和阴影的时候可以选用绘制的方法)
> * 14.优化 UITableView
> 
>  通过正确的设置 reuseIdentifier 来重用 Cell。
> 
> 尽量减少不必要的透明 View。
> 
> 尽量避免渐变效果、图片拉伸和离屏渲染。
> 
> 当不同的行的高度不一样时，尽量缓存它们的高度值。
> 
> 如果 Cell 展示的内容来自网络，确保用异步加载的方式来获取数据，并且缓存服务器的 response。
> 
> 使用 shadowPath 来设置阴影效果。
> 
> 尽量减少 subview 的数量，对于 subview 较多并且样式多变的 Cell，可以考虑用异步绘制或重写 drawRect。
> 
> 尽量优化 - [UITableView tableView:cellForRowAtIndexPath:] 方法中的处理逻辑，如果确实要做一些处理，可以考虑做一次，缓存结果。
> 
> 选择合适的数据结构来承载数据，不同的数据结构对不同操作的开销是存在差异的。
> 
> 对于 rowHeight、sectionFooterHeight、sectionHeaderHeight 尽量使用常量。
> 
> * 15.选择合适的数据存储方式
> 
> 在 iOS 中可以用来进行数据持有化的方案包括：
> 
> NSUserDefaults。只适合用来存小数据。
> 
> XML、JSON、Plist 等文件。JSON 和 XML 文件的差异在「选择正确的数据格式」已经说过了。
> 
> 使用 NSCoding 来存档。NSCoding 同样是对文件进行读写，所以它也会面临必须加载整个文件才能继续的问题。
> 
> 使用 SQLite 数据库。可以配合 FMDB 使用。数据的相对文件来说还是好处很多的，比如可以按需取数据、不用暴力查找等等。
> 
> 使用 CoreData。也是数据库技术，跟 SQLite 的性能差异比较小。但是 CoreData 是一个对象图谱模型，显得更面向对象；SQLite 就是常规的 DBMS。
> 
> * 16.减少应用启动时间
> 
> 快速启动应用对于用户来说可以留下很好的印象。尤其是第一次使用时。
> 
> ------保证应用快速启动的指导原则------：
> 
> 尽量将启动过程中的处理分拆成各个异步处理流，比如：网络请求、数据库访问、数据解析等等。
> 
> 避免臃肿的 XIB 文件，因为它们会在你的主线程中进行加载。重申：Storyboard 没这个问题，放心使用。
> 
> 注意：在测试程序启动性能的时候，最好用与 Xcode 断开连接的设备进行测试。因为 watchdog 在使用 Xcode 进行调试的时候是不会启动的。
> 
> * 17.使用 Autorelease Pool （内存释放池）
> * 18.imageNamed 和 imageWithContentsOfFile

#### 127.runloop 和线程有什么关系?

> * runloop与线程是一一对应的，一个runloop对应一个核心的线程，为什么说是核心的，是因为runloop是可以嵌套的，但是核心的只能有一个，他们的关系保存在一个全局的字典里。
> * runloop是来管理线程的，当线程的runloop被开启后，线程会在执行完任务后进入休眠状态，有了任务就会被唤醒去执行任务。
> * runloop在第一次获取时被创建，在线程结束时被销毁。
> * 对于主线程来说，runloop在程序一启动就默认创建好了。
> * 对于子线程来说，runloop是懒加载的，只有当我们使用的时候才会创建，所以在子线程用定时器要注意：确保子线程的runloop被创建，不然定时器不会回调。

#### 128.介绍下layoutSubview和drawRect

> **layoutSubviews调用情况：**

> * init初始化UIView不会触发调用
> * addSubview会触发调用
> * 改变view的width和height的时候会触发调用
> * 一个UIScrollView滚动会触发调用
> * 旋转screen会触发调用
> * 改变一个UIView大小的时候会触发superView的layoutSubviews事件
> * 直接调用setLayoutSubviews会触发调用
> * -(void)viewWillAppear:(BOOL)animated会触发一次调用
> * -(void)viewDidAppear:(BOOL)animated 看情况，可能有调用
>  
> **drawRect调用情况：**
> 
> * 如果UIView没有设置frame大小，直接导致drawRect不能被自动调用。
> * drawRect在loadView和viewDidLoad这两个方法之后调用
> * 调用sizeToFit后自动调用drawRect
> * 通过设置contentMode值为UIViewContentModeRedraw。那么每次设置或者更改frame自动调用drawRect。
> * 直接调用setNeedsDisplay或者setNeedsDisplayInRect会触发调用

#### 129.写个“标准“宏MIN，这个宏输两个参数并返回较小的那个

> * MIN(A,B) ((A) <= (B) ? (A) : (B))

#### 130.类别有什么作用

> * 1.扩展已有的类（添加方法）
> * 2.可以通过runtime添加属性

#### 131.什么是method swizzing?讲 讲你的使 场景以及使 时的注意事项

> * 1.给扩展添加属性
> * 2.替换系统方法的的实现

#### 132.讲讲iOS事件响应链的原理

> * 1、响应者链通常是由视图（UIView）构成的；
> * 2、一个视图的下一个响应者是它视图控制器（UIViewController）（如果有的话），然后再转给它的父视图（Super View）；
> * 3、视图控制器（如果有的话）的下一个响应者为其管理的视图的父视图；
> * 4、单例的窗口（UIWindow）的内容视图将指向窗口本身作为它的下一个响应者
> * 需要指出的是，Cocoa Touch应用不像Cocoa应用，它只有一个UIWindow对象，因此整个响应者链要简单一点；
> * 5、单例的应用（UIApplication）是一个响应者链的终点，它的下一个响应者指向nil，以结束整个循环。

#### 133.你在什么场景下会选择使 Category

> * 1.扩展已有的类（添加方法）

#### 134.UIview 和CAlayer 是什么关系? 你使用 CLayer做过什么?

> * 首先UIView可以响应事件，Layer不可以.
> * UIView是CALayer的delegate 
> * UIView主要处理事件，CALayer负责绘制就更好 
> * 每个 UIView 内部都有一个 CALayer 在背后提供内容的绘制和显示，并且 UIView 的尺寸样式都由内部的 Layer 所提供。两者都有树状层级结构，layer 内部有 SubLayers，View 内部有 SubViews.但是 Layer 比 View 多了个AnchorPoint
> * 用CAlayer： 创建隐式动画 绘制边框圆角

#### 135.如何处理UITableVier 中Cell 动态计算高度的问题，都有哪些方案?

> * -----第一种方案：------
> * 1、你的Cell要使用AutoLayout来布局约束这是必须的；
> * 2、设置tableview的estimatedRowHeight为一个非零值，这个属性是设置一个预估的高度值，不用太精确。
> * 3、设置tableview的rowHeight属性为UITableViewAutomaticDimension
> * -----第二种方案：-----
> * 1、第三方 UITableView+FDTemplateLayoutCell

#### 136.AutoLayout 中的优先级是什么? UIScrollView 中使用Autolayout 会出现什么问题?

> * 代码计算frame -> autoreszing(父控件和子控件的关系) -> autolayout(任何控件都可以产生关系) -> sizeclass
> * 可以设置两个看似有冲突的约束，但设置不同的优先级之后就不会有冲突了，当其中一个约束失效之后，另一个优先级比较低的约束就会起作用

#### 137.NSIRLConnection 和NSLRLSession 的区别是 么? NSURLProtocol是做什么的?

**1.下载**

* NSURLConnection下载文件时，先是将整个文件下载到内存，然后再写入到沙盒，如果文件比较大，就会出现内存暴涨的情况。
* 而使用NSURLSessionUploadTask下载文件，会默认下载到沙盒中的tem文件中，不会出现内存暴涨的情况，但是在下载完成后会把tem中的临时文件删除，需要在初始化任务方法时，在completionHandler回调中增加保存文件的代码


**2.请求方法的控制**

* NSURLConnection实例化对象，实例化开始，默认请求就发送(同步发送),不需要调用start方法。而cancel可以停止请求的发送，停止后不能继续访问，需要创建新的请求。
* NSURLSession有三个控制方法，取消(cancel)、暂停(suspend)、继续(resume)，暂停以后可以通过继续恢复当前的请求任务。
* 使用NSURLSession进行断点下载更加便捷.
* NSURLSession的构造方法（sessionWithConfiguration:delegate:delegateQueue）中有一个NSURLSessionConfiguration类的参数可以设置配置信息，其决定了cookie，安全和高速缓存策略，最大主机连接数，资源管理，网络超时等配置。NSURLConnection不能进行这个配置，相比较与NSURLConnection依赖与一个全局的配置对象，缺乏灵活性而言，NSURLSession有很大的改进

#### 138.怎么高效的实现控件的圆角效果

> * 绘制圆角

         -(UIImageView *)roundedRectImageViewWithCornerRadius:(CGFloat)cornerRadius {
  
         UIBezierPath *bezierPath = [UIBezierPath 
  
         bezierPathWithRoundedRect:self.bounds cornerRadius:cornerRadius];
  
        CAShapeLayer *layer = [CAShapeLayer layer];
  
         layer.path = bezierPath.CGPath;
  
         self.layer.mask = layer;
  
         return self;
  
         }

#### 139.说说你了解weak属性?

> * weak关键字在OC中属于比较基础的知识此特性表明该属性定义了一种关系“非拥有关系”（nonowning relationship）。为这种属性设置新值得时，设置方法既不保留新值，也不释放旧值。此特性同assign类似，然后在属性所指的对象遭到摧毁时，属性值也会清空（nil out）。
> * 弱引用，不决定对象的存亡。即使一个对象被持有无数个弱引用，只要没有强引用指向它，那么还是会被清除。

#### 140.假如Controller太臃肿，如何优化?

  **1.将网络请求抽象到单独的类中**
>  
> * 方便在基类中处理公共逻辑；
> * 方便在基类中处理缓存逻辑，以及其它一些公共逻辑；
> * 方便做对象的持久化。
>  
  **2.将界面的封装抽象到专门的类中**
>  
> * 构造专门的 UIView 的子类，来负责这些控件的拼装。这是最彻底和优雅的方式，不过稍微麻烦一些的是，你需要把这些控件的事件回调先接管，再都一一暴露回 Controller。
>  
  **3.构造 ViewModel**
>  
> * 借鉴MVVM。具体做法就是将 ViewController 给 View 传递数据这个过程，抽象成构造 ViewModel 的过程。
> 
  **4.专门构造存储类**
>  
> * 专门来处理本地数据的存取。
> 
>  **5.整合常量**

#### 141.项目中网络层如何做安全处理?

> * 1.判断API的调用请求是否来自于经过授权的APP。如若不是则拒绝请求访问
> * 2.在数据请求的过程中进行URL加密处理：防止反编译，接口信息被静态分析。
> * 3.数据传输加密：对客户端传输数据提供有效的加密方案，以防止网络接口的拦截。
> * 如果可以尽量使用HTTPS，可以有效的避免接口数据在传输中被攻击。

#### 142.main()之前的过程有哪些?

> * 在iOS中 main.m 是我们所熟悉的程序入口。但是在在此之前其实程序以及做了很多事了。如系统会获取dyld的路径，并加载。加载程序中的依赖库。调用所有的+ load方法，并返回main函数地址。

#### 143.设计模式是什么？ 你知道哪些设计模式，并简要叙述？

>  设计模式是一种编码经验，就是用比较成熟的逻辑去处理某一种类型的事情。
> 
> *  MVC模式：Model View Control，把模型 视图 控制器 层进行解耦合编写。
> *  MVVM模式：Model View ViewModel 把模型 视图 业务逻辑 层进行解耦和编写。
> *  单例模式：通过static关键词，声明全局变量。在整个进程运行期间只会被赋值一次。
> *  观察者模式：KVO是典型的通知模式，观察某个属性的状态，状态发生变化时通知观察者。
> *  委托模式：代理+协议的组合。实现1对1的反向传值操作。
> *  工厂模式：通过一个类方法，批量的根据已有模板生产对象。

#### 144.MVC 和 MVVM 的区别

> *  MVVM是对胖模型进行的拆分，其本质是给控制器减负，将一些弱业务逻辑放到VM中去处理。
> *  MVC是一切设计的基础，所有新的设计模式都是基于MVC进行的改进。

#### 145.Objective-C的类可以多重继承么？可以实现多个接口么？Category是什么？重写一个类的方式用继承好还是分类好？为什么？

> * Objective-C的类不可以多重继承；可以实现多个接口（协议）；Category是类别；一般情况用分类好，用Category去重写类的方法，仅对本Category有效，不会影响到其他类与原有类的关系。

#### 146。@property 的本质是什么？ivar、getter、setter 是如何生成并添加到这个类中的

> * @property 的本质是什么？
> * @property = ivar + getter + setter;
> * “属性” (property)有两大概念：ivar（实例变量）、getter+setter（存取方法）
> * “属性” (property)作为 Objective-C 的一项特性，主要的作用就在于封装对象中的数据。 Objective-C 对象通常会把其所需要的数据保存为各种实例变量。实例变量一般通过“存取方法”(access method)来访问。其中，“获取方法” (getter)用于读取变量值，而“设置方法” (setter)用于写入变量值。

#### 147.@property中有哪些属性关键字？/ @property 后面可以有哪些修饰符？

> 属性可以拥有的特质分为四类:
> 
> * 1.原子性：nonatomic、atomic特质
> * 2.读/写权限：readwrite(读写)、readonly (只读)
> * 3.内存管理语义：assign、strong、 weak、unsafe_unretained、copy
> * 4.方法名：getter=<name> 、setter=<name>
> * 5.不常用的：nonnull,null_resettable,nullable
> 

#### 148.什么情况使用 weak 关键字，相比 assign 有什么不同？

> * 1.在 ARC 中,在有可能出现循环引用的时候,往往要通过让其中一端使用 weak 来解决,比如: delegate 代理属性。
> * 2.自身已经对它进行一次强引用,没有必要再强引用一次,此时也会使用 weak,自定义 IBOutlet 控件属性一般也使用 weak；当然，也可以使用strong。
> * 3.IBOutlet连出来的视图属性为什么可以被设置成weak?
> 
>  因为父控件的subViews数组已经对它有一个强引用。
> * 4.不同点：
> 
>  assign 可以用非 OC 对象，而 weak 必须用于 OC 对象。
> 
>  weak 表明该属性定义了一种“非拥有关系”。在属性所指的对象销毁时，属性值会自动清空(nil)。

#### 149.怎么用 copy 关键字？

> * NSString、NSArray、NSDictionary 等等经常使用copy关键字，是因为他们有对应的可变类型：NSMutableString、NSMutableArray、NSMutableDictionary；
> * block 也经常使用 copy 关键字。
> * 说明：
>  block 使用 copy 是从 MRC 遗留下来的“传统”,在 MRC 中,方法内部的 block 是在栈区的,使用 copy 可以把它放到堆区.在 ARC 中写不写都行：对于 block 使用 copy 还是 strong 效果是一样的，但写上 copy 也无伤大雅，还能时刻提醒我们：编译器自动对 block 进行了 copy 操作。如果不写 copy ，该类的调用者有可能会忘记或者根本不知道“编译器会自动对 block 进行了 copy 操作”，他们有可能会在调用之前自行拷贝属性值。这种操作多余而低效。

#### 150.用@property声明的 NSString / NSArray / NSDictionary 经常使用 copy 关键字，为什么？如果改用strong关键字，可能造成什么问题？

> * 用 @property 声明 NSString、NSArray、NSDictionary 经常使用 copy 关键字，是因为他们有对应的可变类型：NSMutableString、NSMutableArray、NSMutableDictionary，他们之间可能进行赋值操作（就是把可变的赋值给不可变的），为确保对象中的字符串值不会无意间变动，应该在设置新属性值时拷贝一份。
> * 因为父类指针可以指向子类对象,使用 copy 的目的是为了让本对象的属性不受外界影响,使用 copy 无论给我传入是一个可变对象还是不可对象,我本身持有的就是一个不可变的副本。
> * 如果我们使用是 strong ,那么这个属性就有可能指向一个可变对象,如果这个可变对象在外部被修改了,那么会影响该属性。
> * 总结：使用copy的目的是，防止把可变类型的对象赋值给不可变类型的对象时，可变类型对象的值发送变化会无意间篡改不可变类型对象原来的值。

#### 151.系统对象的 copy 与 mutableCopy 方法

*不管是集合类对象（NSArray、NSDictionary、NSSet ... 之类的对象），还是非集合类对象（NSString, NSNumber ... 之类的对象），接收到copy和mutableCopy消息时，都遵循以下准则：*

> * copy 返回的是不可变对象（immutableObject）；如果用copy返回值调用mutable对象的方法就会crash。
> * mutableCopy 返回的是可变对象（mutableObject）。
> 
  **一、非集合类对象的copy与mutableCopy**
> 
> * 在非集合类对象中，对不可变对象进行copy操作，是指针复制，mutableCopy操作是内容复制；
> * 对可变对象进行copy和mutableCopy都是内容复制。用代码简单表示如下：
 
          NSString *str = @"hello word!";
  
          NSString *strCopy = [str copy] // 指针复制，strCopy与str的地址一样
  
          NSMutableString *strMCopy = [str mutableCopy] // 内容复制，strMCopy与str的地址不一样


--

         NSMutableString *mutableStr = [NSMutableString stringWithString: @"hello word!"];
  
         NSString *strCopy = [mutableStr copy] // 内容复制
  
         NSMutableString *strMCopy = [mutableStr mutableCopy] // 内容复制
>  

 **二、集合类对象的copy与mutableCopy (同上)**

>    
> * 在集合类对象中，对不可变对象进行copy操作，是指针复制，mutableCopy操作是内容复制；
> * 对可变对象进行copy和mutableCopy都是内容复制。但是：集合对象的内容复制仅限于对象本身，对集合内的对象元素仍然是指针复制。(即单层内容复制)

>         NSArray *arr = @[@[@"a", @"b"], @[@"c", @"d"];
> 
>         NSArray *copyArr = [arr copy]; // 指针复制
> 
>         NSMutableArray *mCopyArr = [arr mutableCopy]; //单层内容复制
> 
>         NSMutableArray *array = [NSMutableArray arrayWithObjects:
> 
>         [NSMutableString stringWithString:@"a"],@"b",@"c",nil];
> 
>         NSArray *copyArr = [mutableArr copy]; // 单层内容复制

> * 【总结一句话】：
> * 只有对不可变对象进行copy操作是指针复制（浅复制），其它情况都是内容复制（深复制）！

#### 152.这个写法会出什么问题：@property (nonatomic, copy) NSMutableArray *arr;

> * 问题：添加,删除,修改数组内的元素的时候,程序会因为找不到对应的方法而崩溃。
 
         //如：-[__NSArrayI removeObjectAtIndex:]: unrecognized selector sent to instance 0x7fcd1bc30460
 
         // cop	y后返回的是不可变对象（即 arr 是 NSArray 类型，NSArray 类型对象不能调用 NSMutableArray 类型对象的方法）
 
> * 原因：是因为 copy 就是复制一个不可变 NSArray 的对象，不能对 NSArray 对象进行添加/修改。

#### 153.如何让自己的类用 copy 修饰符？如何重写带 copy 关键字的 setter？

> * 若想令自己所写的对象具有拷贝功能，则需实现 NSCopying 协议。如果自定义的对象分为可变版本与不可变版本，那么就要同时实现 NSCopying 与 NSMutableCopying 协议。
> * 具体步骤：

> 需声明该类遵从 NSCopying 协议
> 
> 实现 NSCopying 协议的方法。
> 
>     // 该协议只有一个方法: - (id)copyWithZone:(NSZone *)zone;
> 
>     // 注意：使用 copy 修饰符，调用的是copy方法，其实真正需要实现的是 “copyWithZone” 方法。
> 

#### 154.写一个 setter 方法用于完成 @property (nonatomic, retain) NSString *name，写一个 setter 方法用于完成 @property (nonatomic, copy) NSString *name
      // retain
	 
	 -(void)setName:(NSArray *)name
	 
	 {
	 
	     if (_name !=name) {
	         [_name release];
	         _name =[name retain];
	         
	     }
	     
	 }
	
	 // copy
	 
	 -(void)setName:(NSString *)name
	 
	 {
	 
	     if (_name !=name) {
	        [_name release];
	         _name = [name copy];
	     }
	     
	 }
#### 155.@synthesize 和 @dynamic 分别有什么作用？

> * @property有两个对应的词，一个是@synthesize（合成实例变量），一个是@dynamic。
> * 如果@synthesize和@dynamic都没有写，那么默认的就是 @synthesize var = _var;
> * // 在类的实现代码里通过 @synthesize 语法可以来指定实例变量的名字。(@synthesize var = _newVar;)
> * @synthesize 的语义是如果你没有手动实现setter方法和getter方法，那么编译器会自动为你加上这两个方法。
> * @dynamic 告诉编译器，属性的setter与getter方法由用户自己实现，不自动生成（如，@dynamic var）。

#### 156.常见的 Objective-C 的数据类型有那些，和C的基本数据类型有什么区别？如：NSInteger和int

> * Objective-C的数据类型有NSString，NSNumber，NSArray，NSMutableArray，NSData等等，这些都是class，创建后便是对象；
> * 而C语言的基本数据类型int，只是一定字节的内存空间，用于存放数值;
> * NSInteger是基本数据类型，并不是NSNumber的子类，当然也不是NSObject的子类。NSInteger是基本数据类型Int或者Long的别名(NSInteger的定义typedef long NSInteger)，它的区别在于，NSInteger会根据系统是32位还是64位来决定是本身是int还是long。

#### 157.id 声明的对象有什么特性？

> * id 声明的对象具有运行时的特性，即可以指向任意类型的Objcetive-C的对象。

#### 158.Objective-C 如何对内存管理的，说说你的看法和解决方法？

> *Objective-C的内存管理主要有三种方式ARC(自动内存计数)、手动内存计数、内存池。*
> 
> * (Garbage Collection)自动内存计数：这种方式和java类似，在你的程序的执行过程中。始终有一个高人在背后准确地帮你收拾垃圾，你不用考虑它什么时候开始工作，怎样工作。你只需要明白，我申请了一段内存空间，当我不再使用从而这段内存成为垃圾的时候，我就彻底的把它忘记掉，反正那个高人会帮我收拾垃圾。遗憾的是，那个高人需要消耗一定的资源，在携带设备里面，资源是紧俏商品所以iPhone不支持这个功能。所以“Garbage Collection”不是本入门指南的范围，对“Garbage Collection”内部机制感兴趣的同学可以参考一些其他的资料，不过说老实话“Garbage Collection”不大适合适初学者研究。
> 
> 解决: 通过alloc – initial方式创建的, 创建后引用计数+1, 此后每retain一次引用计数+1, 那么在程序中做相应次数的release就好了.
> 
> *  (Reference Counted)手动内存计数：就是说，从一段内存被申请之后，就存在一个变量用于保存这段内存被使用的次数，我们暂时把它称为计数器，当计数器变为0的时候，那么就是释放这段内存的时候。比如说，当在程序A里面一段内存被成功申请完成之后，那么这个计数器就从0变成1(我们把这个过程叫做alloc)，然后程序B也需要使用这个内存，那么计数器就从1变成了2(我们把这个过程叫做retain)。紧接着程序A不再需要这段内存了，那么程序A就把这个计数器减1(我们把这个过程叫做release);程序B也不再需要这段内存的时候，那么也把计数器减1(这个过程还是release)。当系统(也就是Foundation)发现这个计数器变 成员了0，那么就会调用内存回收程序把这段内存回收(我们把这个过程叫做dealloc)。顺便提一句，如果没有Foundation，那么维护计数器，释放内存等等工作需要你手工来完成。
> 
> 解决:一般是由类的静态方法创建的, 函数名中不会出现alloc或init字样, 如[NSString string]和[NSArray arrayWithObject:], 创建后引用计数+0, 在函数出栈后释放, 即相当于一个栈上的局部变量. 当然也可以通过retain延长对象的生存期.
> 
> *  (NSAutoRealeasePool)内存池：可以通过创建和释放内存池控制内存申请和回收的时机.
> 
> 解决:是由autorelease加入系统内存池, 内存池是可以嵌套的, 每个内存池都需要有一个创建释放对, 就像main函数中写的一样. 使用也很简单, 比如[[[NSString alloc]initialWithFormat:@”Hey you!”] autorelease], 即将一个NSString对象加入到最内层的系统内存池, 当我们释放这个内存池时, 其中的对象都会被释放.

#### 159.Category（类别）、 Extension（扩展）和继承的区别

> * 分类有名字，类扩展没有分类名字，是一种特殊的分类。
> * 分类只能扩展方法（属性仅仅是声明，并没真正实现），类扩展可以扩展属性、成员变量和方法。
> * 继承可以增加，修改或者删除方法，并且可以增加属性。

#### 160.我们说的OC是动态运行时语言是什么意思？

> * 主要是将数据类型的确定由编译时，推迟到了运行时。简单来说, 运行时机制使我们直到运行时才去决定一个对象的类别,以及调用该类别对象指定方法。

#### 161.为什么我们常见的delegate属性都用是weak而不是retain/strong？

> * 是为了防止delegate两端产生不必要的循环引用。
> * @property (nonatomic, weak) id<UITableViewDelegate> delegate;

#### 162.什么时候用delete，什么时候用Notification？

> * Delegate(委托模式)：1对1的反向消息通知功能。
> * Notification(通知模式)：只想要把消息发送出去，告知某些状态的变化。但是并不关心谁想要知道这个。

#### 163.什么是 KVO 和 KVC？

> *  KVC(Key-Value-Coding)：键值编码 是一种通过字符串间接访问对象的方式（即给属性赋值）
> 
> 举例说明：
> 
>     stu.name = @"张三" // 点语法给属性赋值
> 
>     [stu setValue:@"张三" forKey:@"name"]; // 通过字符串使用KVC方式给属性赋值
> 
>     stu1.nameLabel.text = @"张三";
> 
>     [stu1 setValue:@"张三" forKey:@"nameLabel.text"]; // 跨层赋值
> 
> *  KVO(key-Value-Observing)：键值观察机制 他提供了观察某一属性变化的方法，极大的简化了代码。
> 
> KVO只能被KVC触发，包括使用setValue:forKey:方法和点语法。
> 
>      // 通过下方方法为属性添加KVO观察
> 
>      -(void)addObserver:(NSObject *)observer forKeyPath:(NSString *)keyPath 
>  
>      options:(NSKeyValueObservingOptions)options context:(nullable void 
>  
>      *)context;
>  
>      // 当被观察的属性发送变化时，会自动触发下方方法
>  
>      -(void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object
>  
>       change:(NSDictionary *)change context:(void *)context{}
>   
>       // KVC 和 KVO 的 keyPath 可以是属性、实例变量、成员变量。

#### 164.KVC的底层实现？

> *当一个对象调用setValue方法时，方法内部会做以下操作：*
> 
> *  检查是否存在相应的key的set方法，如果存在，就调用set方法。
> *  如果set方法不存在，就会查找与key相同名称并且带下划线的成员变量，如果有，则直接给成员变量属性赋值。
> *  如果没有找到_key，就会查找相同名称的属性key，如果有就直接赋值。
> *  如果还没有找到，则调用valueForUndefinedKey:和setValue:forUndefinedKey:方法。这些方法的默认实现都是抛出异常，我们可以根据需要重写它们。

#### 165.KVO的底层实现？

> * KVO基于runtime机制实现。

#### 166. ViewController生命周期

> *按照执行顺序排列：*
> 
> * 1.initWithCoder：通过nib文件初始化时触发。
> * 2.awakeFromNib：nib文件被加载的时候，会发生一个awakeFromNib的消息到nib文件中的每个对象。
> * 3.loadView：开始加载视图控制器自带的view。
> * 4.viewDidLoad：视图控制器的view被加载完成。
> * 5.viewWillAppear：视图控制器的view将要显示在window上。
> * 6.updateViewConstraints：视图控制器的view开始更新AutoLayout约束。
> * 7.viewWillLayoutSubviews：视图控制器的view将要更新内容视图的位置。
> * 8.viewDidLayoutSubviews：视图控制器的view已经更新视图的位置。
> * 9.viewDidAppear：视图控制器的view已经展示到window上。
> * 10.viewWillDisappear：视图控制器的view将要从window上消失。
> * 11.viewDidDisappear：视图控制器的view已经从window上消失。

#### 167.你是否接触过OC中的反射机制？简单聊一下概念和使用

> *  class反射
> 
> 通过类名的字符串形式实例化对象。
> 
>     Class class = NSClassFromString(@"student");
> 
>     Student *stu = [[class alloc] init];
> 
> 将类名变为字符串。
> 
>     Class class =[Student class];
> 
>     NSString className = NSStringFromClass(class);
> 
> *  SEL的反射
> 
> 通过方法的字符串形式实例化方法。
> 
>     SEL selector = NSSelectorFromString(@"setName");
> 
>     [stu performSelector:selector withObject:@"Mike"];
> 
> 将方法变成字符串。
> 
>     NSStringFromSelector(@selector(setName:));
> 

#### 168.调用方法有两种方式：

> *  直接通过方法名来调用。[person show];
> *  间接的通过SEL数据来调用 SEL aaa = @selector(show); [person performSelector:aaa];

#### 169.如何对iOS设备进行性能测试？

> * Profile-> Instruments ->Time Profiler

#### 170.开发项目时你是怎么检查内存泄露？

> *  静态分析 analyze。
> *  instruments工具里面有个leak可以动态分析。

#### 171.什么是懒加载？

> * 懒加载就是只在用到的时候才去初始化。也可以理解成延时加载。
> * 我觉得最好也最简单的一个例子就是tableView中图片的加载显示了, 一个延时加载, 避免内存过高,一个异步加载,避免线程堵塞提高用户体验。

#### 172.类变量的 @public，@protected，@private，@package 声明各有什么含义？

> * @public 任何地方都能访问;
> * @protected 该类和子类中访问,是默认的;
> * @private 只能在本类中访问;
> * @package 本包内使用,跨包不可以。

#### 173.什么是谓词？

> * 谓词就是通过NSPredicate给定的逻辑条件作为约束条件,完成对数据的筛选。
> 
>  //定义谓词对象,谓词对象中包含了过滤条件(过滤条件比较多)
> 
>      NSPredicate *predicate = [NSPredicate predicateWithFormat:@"age<%d",30];
> 
>  //使用谓词条件过滤数组中的元素,过滤之后返回查询的结果
> 
>      NSArray *array = [persons filteredArrayUsingPredicate:predicate];
> 

#### 174.isa指针问题

> * isa：是一个Class 类型的指针. 每个实例对象有个isa的指针,他指向对象的类,而Class里也有个isa的指针, 指向meteClass(元类)。元类保存了类方法的列表。当类方法被调 用时,先会从本身查找类方法的实现,如果没有,元类会向他父类查找该方法。同时注意的是:元类(meteClass)也是类,它也是对象。元类也有isa指针,它的isa指针最终指向的是一个根元类(root meteClass)。根元类的isa指针指向本身,这样形成了一个封闭的内循环。

#### 175.如何访问并修改一个类的私有属性？

> *  一种是通过KVC获取。
> *  通过runtime访问并修改私有属性。

#### 176.一个objc对象的isa的指针指向什么？有什么作用？

> * 指向他的类对象,从而可以找到对象上的方法。

#### 177.下面的代码输出什么？

     @implementation Son : Father
 
     - (id)init {
        if (self = [super init]) {
            NSLog(@"%@", NSStringFromClass([self class])); // Son
            NSLog(@"%@", NSStringFromClass([super class])); // Son
        }
        return self;
     }
     @end
     
>     // 解析：
>     
>     self 是类的隐藏参数，指向当前调用方法的这个类的实例。
>     super是一个Magic Keyword，它本质是一个编译器标示符，和self是指向的同一个消息接收者。
>     不同的是：super会告诉编译器，调用class这个方法时，要去父类的方法，而不是本类里的。
>     上面的例子不管调用[self class]还是[super class]，接受消息的对象都是当前 Son *obj 这个对象。

#### 178.写一个完整的代理，包括声明、实现

      // 创建
  
     @protocol MyDelagate
     
     @required
     
     -(void)eat:(NSString *)foodName; 
     
     @optional
     
     -(void)run;
     
     @end
 
     //  声明 .h
     
     @interface person: NSObject<MyDelagate>
 
     @end
 
      //  实现 .m
     
         @implementation person
     
         -(void)eat:(NSString *)foodName { 
       
         NSLog(@"吃:%@!", foodName);
        
         } 
     
          -(void)run {
     
          NSLog(@"run!");
        
         }
 
     @end

#### 179.isKindOfClass、isMemberOfClass、selector作用分别是什么

> * isKindOfClass：作用是某个对象属于某个类型或者继承自某类型。
> * isMemberOfClass：某个对象确切属于某个类型。
> * selector：通过方法名，获取在内存中的函数的入口地址。

#### 180.delegate 和 notification 的区别

> *  二者都用于传递消息，不同之处主要在于一个是一对一的，另一个是一对多的。
> *  notification通过维护一个array，实现一对多消息的转发。
> *  delegate需要两者之间必须建立联系，不然没法调用代理的方法；notification不需要两者之间有联系。

#### 181.什么是block？

> *  闭包（block）：闭包就是获取其它函数局部变量的匿名函数。

#### 182.block反向传值

在控制器间传值可以使用代理或者block，使用block相对来说简洁。

    在前一个控制器的touchesBegan:方法内实现如下代码。

      // OneViewController.m
      TwoViewController *twoVC = [[TwoViewController alloc] init];
      twoVC.valueBlcok = ^(NSString *str) {
        NSLog(@"OneViewController拿到值：%@", str); 
      };
      [self presentViewController:twoVC animated:YES completion:nil];

      // TwoViewController.h   （在.h文件中声明一个block属性）
      @property (nonatomic ,strong) void(^valueBlcok)(NSString *str);

      // TwoViewController.m   （在.m文件中实现方法）
    - (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event          {
        // 传值:调用block
        if (_valueBlcok) {
            _valueBlcok(@"123456");
        }
    }

#### 183.block的注意点

在block内部使用外部指针且会造成循环引用情况下，需要用__week修饰外部指针：

        __weak typeof(self) weakSelf = self; 
        
在block内部如果调用了延时函数还使用弱指针会取不到该指针，因为已经被销毁了，需要在
block内部再将弱指针重新强引用一下。

        __strong typeof(self) strongSelf = weakSelf;
 如果需要在block内部改变外部栈区变量的话，需要在用__block修饰外部变量。

#### 184.lldb（gdb）常用的控制台调试命令？

> *  p 输出基本类型。是打印命令，需要指定类型。是print的简写
>    p (int)[[[self view] subviews] count]
> *  po 打印对象，会调用对象description方法。是print-object的简写
>   po [self view]
> *  expr 可以在调试时动态执行指定表达式，并将结果打印出来。常用于在调试过程中修改变量的值。
> *  bt：打印调用堆栈，是thread backtrace的简写，加all可打印所有thread的堆栈
> *  br l：是breakpoint list的简写

#### 185.你一般是怎么用Instruments的？

 *Instruments里面工具很多，常用：*
 
> * Time Profiler: 性能分析
> * Zombies：检查是否访问了僵尸对象，但是这个工具只能从上往下检查，不智能。
> * Allocations：用来检查内存，写算法的那批人也用这个来检查。
> * Leaks：检查内存，看是否有内存泄露。

#### 186.iOS的沙盒目录结构是怎样的？

 *沙盒结构：*
 
> * Application：存放程序源文件，上架前经过数字签名，上架后不可修改。
> * Documents：常用目录，iCloud备份目录，存放数据。（这里不能存缓存文件，否则上架不被通过）
> * Library：
>          Caches：存放体积大又不需要备份的数据。(常用的缓存路径)
>          Preference：设置目录，iCloud会备份设置信息。
> * tmp：存放临时文件，不会被备份，而且这个文件下的数据有可能随时被清除的可能。

#### 187.iOS多线程技术有哪几种方式？

> * pthread、NSThread、GCD、NSOperation

#### 188.GCD 与 NSOperation 的区别：

> * GCD 和 NSOperation 都是用于实现多线程：
> *     GCD 基于C语言的底层API，GCD主要与block结合使用，代码简洁高效。
> *     NSOperation 属于Objective-C类，是基于GCD更高一层的封装。复杂任务一般用NSOperation实现。

#### 189.写出使用GCD方式从子线程回到主线程的方法代码

		dispatch_async(dispatch_get_main_queue(), ^{ });

#### 190.如何用GCD同步若干个异步调用？（如根据若干个url异步加载多张图片，然后在都下载完成后合成一张整图）

	// 使用Dispatch Group追加block到Global Group Queue,这些block如果全部执行完毕，就会执行Main Dispatch Queue中的结束处理的block。
	// 创建队列组
	dispatch_group_t group = dispatch_group_create();
	// 获取全局并发队列
	dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
	dispatch_group_async(group, queue, ^{ /*加载图片1 */ });
	dispatch_group_async(group, queue, ^{ /*加载图片2 */ });
	dispatch_group_async(group, queue, ^{ /*加载图片3 */ }); 
	// 当并发队列组中的任务执行完毕后才会执行这里的代码
	dispatch_group_notify(group, dispatch_get_main_queue(), ^{
	        // 合并图片
	});

#### 191.dispatch_barrier_async（栅栏函数）的作用是什么？

	函数定义：dispatch_barrier_async(dispatch_queue_t queue, dispatch_block_t block);
	作用：
	    1.在它前面的任务执行结束后它才执行，它后面的任务要等它执行完成后才会开始执行。
	    2.避免数据竞争
	
	 // 1.创建并发队列
	 dispatch_queue_t queue = dispatch_queue_create("myQueue", DISPATCH_QUEUE_CONCURRENT);
	// 2.向队列中添加任务
	dispatch_async(queue, ^{  // 1.2是并行的
	    NSLog(@"任务1, %@",[NSThread currentThread]);
	});
	dispatch_async(queue, ^{
	    NSLog(@"任务2, %@",[NSThread currentThread]);
	});
	
	 dispatch_barrier_async(queue, ^{
	    NSLog(@"任务 barrier, %@", [NSThread currentThread]);
	});
	
	 dispatch_async(queue, ^{   // 这两个是同时执行的
	    NSLog(@"任务3, %@",[NSThread currentThread]);
	});
	 dispatch_async(queue, ^{
	    NSLog(@"任务4, %@",[NSThread currentThread]);
	});
	
	 // 输出结果: 任务1 任务2 ——》 任务 barrier ——》任务3 任务4 
	// 其中的任务1与任务2，任务3与任务4 由于是并行处理先后顺序不定。

#### 192.以下代码运行结果如何？

	- (void)viewDidLoad {
	    [super viewDidLoad];
	    NSLog(@"1");
	    dispatch_sync(dispatch_get_main_queue(), ^{
	        NSLog(@"2");
	    });
	    NSLog(@"3");
	 }
	// 只输出：1。（主线程死锁）

#### 193.什么是 RunLoop

> * 从字面上讲就是运行循环，它内部就是do-while循环，在这  个循环内部不断地处理各种任务。
> * 一个线程对应一个RunLoop，基本作用就是保持程序的持续运行，处理app中的各种事件。通过runloop，有事运行，没事就休息，可以节省cpu资源，提高程序性能。
>  
> *   主线程的run loop默认是启动的。iOS的应用程序里面，程序启动后会有一个如下的main()函数
  
	  int main(int argc, char * argv[]) {
	     @autoreleasepool {
	         return UIApplicationMain(argc, argv, nil,   NSStringFromClass([AppDelegate class]));
	     }
	 }

#### 194.什么是 Runtime

> * Runtime又叫运行时，是一套底层的C语言API，其为iOS内部的核心之一，我们平时编写的OC代码，底层都是基于它来实现的。

#### 195.Runtime实现的机制是什么，怎么用，一般用于干嘛？

> * 使用时需要导入的头文件 <objc/message.h> <objc/runtime.h>
> * Runtime 运行时机制，它是一套C语言库。
> * 实际上我们编写的所有OC代码，最终都是转成了runtime库的东西。
> 
>   比如：
> 
>   类转成了 Runtime 库里面的结构体等数据类型，
> 
>   方法转成了 Runtime 库里面的C语言函数，
> 
>   平时调方法都是转成了 objc_msgSend 函数（所以说OC有个消息发送机制）
 
     // OC是动态语言，每个方法在运行时会被动态转为消息发送，即：objc_msgSend(receiver, selector)。
     // [stu show];  在objc动态编译时，会被转意为：objc_msgSend(stu, @selector(show));   
      
> * 因此，可以说 Runtime 是OC的底层实现，是OC的幕后执行者。

#### 196.有了Runtime库，能做什么事情呢？

> *  Runtime库里面包含了跟类、成员变量、方法相关的API。
> *  比如：
> 
>      （1）获取类里面的所有成员变量。
> 
>      （2）为类动态添加成员变量。
> 
>      （3）动态改变类的方法实现。
> 
>      （4）为类动态添加新的方法等。
> 
> *  因此，有了Runtime，想怎么改就怎么改。

#### 197.什么是 Method Swizzle（黑魔法），什么情况下会使用？

> * 在没有一个类的实现源码的情况下，想改变其中一个方法的实现，除了继承它重写、和借助类别重名方法暴力抢先之外，还有更加灵活的方法 Method Swizzle。
> * Method Swizzle 指的是改变一个已存在的选择器对应的实现的过程。OC中方法的调用能够在运行时通过改变，通过改变类的调度表中选择器到最终函数间的映射关系。
> * 在OC中调用一个方法，其实是向一个对象发送消息，查找消息的唯一依据是selector的名字。利用OC的动态特性，可以实现在运行时偷换selector对应的方法实现。
> * 每个类都有一个方法列表，存放着selector的名字和方法实现的映射关系。IMP有点类似函数指针，指向具体的方法实现。
> * 我们可以利用 method_exchangeImplementations 来交换2个方法中的IMP。
> * 我们可以利用 class_replaceMethod 来修改类。
> * 我们可以利用 method_setImplementation 来直接设置某个方法的IMP。
> * 归根结底，都是偷换了selector的IMP。

#### 198._objc_msgForward 函数是做什么的，直接调用它将会发生什么？

> * _objc_msgForward是 IMP 类型，用于消息转发的：当向一个对象发送一条消息，但它并没有实现的时候，_objc_msgForward会尝试做消息转发。

#### 199.什么是 TCP / UDP ?

> * TCP：传输控制协议。
> * UDP：用户数据协议。
>  
> * TCP 是面向连接的，建立连接需要经历三次握手，是可靠的传输层协议。
> * UDP 是面向无连接的，数据传输是不可靠的，它只管发，不管收不收得到。
> * 简单的说，TCP注重数据安全，而UDP数据传输快点，但安全性一般。
>  
> *  通信底层原理（OSI七层模型）
> 
>  OSI采用了分层的结构化技术，共分七层：
> 
>   物理层、数据链路层、网络层、传输层、会话层、表示层、应用层。

#### 200.介绍一下XMPP？
> 
> * XMPP是一种以XML为基础的开放式实时通信协议。
> * 简单的说，XMPP就是一种协议，一种规定。就是说，在网络上传东西，XMM就是规定你上传大小的格式。

#### 201.OC中创建线程的方法是什么？如果在主线程中执行代码，方法是什么？

	// 创建线程的方法
	 - [NSThread detachNewThreadSelector:nil toTarget:nil withObject:nil]
	 - [self performSelectorInBackground:nil withObject:nil];
	 - [[NSThread alloc] initWithTarget:nil selector:nil object:nil];
	 - dispatch_async(dispatch_get_global_queue(0, 0), ^{});
	 - [[NSOperationQueue new] addOperation:nil];
	
	  // 主线程中执行代码的方法
	 - [self performSelectorOnMainThread:nil withObject:nil waitUntilDone:YES];
	 - dispatch_async(dispatch_get_main_queue(), ^{});
	 - [[NSOperationQueue mainQueue] addOperation:nil];

#### 202.tableView的重用机制？

> * UITableView 通过重用单元格来达到节省内存的目的: 通过为每个单元格指定一个重用标识符，即指定了单元格的种类,当屏幕上的单元格滑出屏幕时，系统会把这个单元格添加到重用队列中，等待被重用，当有新单元格从屏幕外滑入屏幕内时，从重用队列中找看有没有可以重用的单元格，如果有，就拿过来用，如果没有就创建一个来使用。

#### 203.用伪代码写一个线程安全的单例模式

	static id _instance;
	 + (id)allocWithZone:(struct _NSZone *)zone {
	    static dispatch_once_t onceToken;
	    dispatch_once(&onceToken, ^{
	        _instance = [super allocWithZone:zone];
	    });
	    return _instance;
	 }
	
	 + (instancetype)sharedData {
	    static dispatch_once_t onceToken;
	    dispatch_once(&onceToken, ^{
	        _instance = [[self alloc] init];
	    });
	    return _instance;
	 }
	
	 - (id)copyWithZone:(NSZone *)zone {
	    return _instance;
	 }

#### 204.如何实现视图的变形?

> * 通过修改view的 transform 属性即可。

#### 205.字符串常用方法：

	 NSString *str = @"abc*123";
	 NSArray *arr = [str componentsSeparatedByString:@"*"]; //以目标字符串把原 字符串分割成两部分，存到数组中。@[@"abc", @"123"];

#### 206.如何高性能的给 UIImageView 加个圆角?

> * 不好的解决方案：使用下面的方式会强制Core Animation提前渲染屏幕的离屏绘制, 而离屏绘制就会给性能带来负面影响，会有卡顿的现象出现。

    self.view.layer.cornerRadius = 5.0f;
    self.view.layer.masksToBounds = YES;

>  **正确的解决方案：使用绘图技术**

     - (UIImage *)circleImage {
        // NO代表透明
         UIGraphicsBeginImageContextWithOptions(self.size, NO, 0.0);
         // 获得上下文
         CGContextRef ctx = UIGraphicsGetCurrentContext();
         // 添加一个圆
         CGRect rect = CGRectMake(0, 0, self.size.width, self.size.height);
         CGContextAddEllipseInRect(ctx, rect);
        // 裁剪
        CGContextClip(ctx);
         // 将图片画上去
         [self drawInRect:rect];
         UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
         // 关闭上下文
         UIGraphicsEndImageContext();
         return image;
     }

> 还有一种方案：使用了贝塞尔曲线"切割"个这个图片, 给UIImageView 添加了的圆角，其实也是通过绘图技术来实现的。

     UIImageView *imageView = [[UIImageView alloc] initWithFrame:CGRectMake(0, 0, 100, 100)];
     imageView.center = CGPointMake(200, 300);
     UIImage *anotherImage = [UIImage imageNamed:@"image"];
     UIGraphicsBeginImageContextWithOptions(imageView.bounds.size, NO, 1.0);
     [[UIBezierPath bezierPathWithRoundedRect:imageView.bounds
                           cornerRadius:50] addClip];
     [anotherImage drawInRect:imageView.bounds];
     imageView.image = UIGraphicsGetImageFromCurrentImageContext();
     UIGraphicsEndImageContext();
     [self.view addSubview:imageView];

#### 207.你是怎么封装一个view的

> * 可以通过纯代码或者xib的方式来封装子控件
> * 建立一个跟view相关的模型，然后将模型数据传给view，通过模型上的数据给view的子控件赋值

	 /**
	  *  纯代码初始化控件时一定会走这个方法
	  */
	 - (instancetype)initWithFrame:(CGRect)frame {
	    if(self = [super initWithFrame:frame]) {
	         [self setupUI];
	     }
	     return self;
	 }
	
	 /**
	  *  通过xib初始化控件时一定会走这个方法
	  */
	 - (id)initWithCoder:(NSCoder *)aDecoder {
	    if(self = [super initWithCoder:aDecoder]) {
	         [self setupUI];
	     }
	     return self;
	 }
	
	 - (void)setupUI {
	     // 初始化代码
	 }

#### 208.HTTP协议中 POST 方法和 GET 方法有那些区别?

> *  GET用于向服务器请求数据，POST用于提交数据
> * GET请求，请求参数拼接形式暴露在地址栏，而POST请求参数则放在请求体里面，因此GET请求不适合用于验证密码等操作
> * GET请求的URL有长度限制，POST请求不会有长度限制

#### 209.请简单的介绍下APNS发送系统消息的机制

> * APNS优势：杜绝了类似安卓那种为了接受通知不停在后台唤醒程序保持长连接的行为，由iOS系统和APNS进行长连接替代。
> * APNS的原理：
> 
>   应用在通知中心注册，由iOS系统向APNS请求返回设备令牌(device Token)
>     
>   应用程序接收到设备令牌并发送给自己的后台服务器
>     
>   服务器把要推送的内容和设备发送给APNS
>     
>   APNS根据设备令牌找到设备，再由iOS根据APPID把推送内容展示

#### 210.对于语句NSString*obj = [[NSData alloc] init]; obj在编译时和运行时分别时什么类型的对象?

> * 编译时是NSString的类型;运行时是NSData类型的对象

#### 211.原子(atomic)跟非原子(non-atomic)属性有什么区别?

> *  atomic提供多线程安全。是防止在写未完成的时候被另外一个线程读取，造成数据错误
>  
> *  non-atomic:在自己管理内存的环境中，解析的访问器保留并自动释放返回的值，如果指定了 nonatomic ，那么访问器只是简单地返回这个值。

#### 212.看下面的程序,第一个NSLog会输出什么?这时str的retainCount是多少?第二个和第三个呢? 为什么?

	NSMutableArray* ary = [[NSMutableArray array] retain];
	NSString *str = [NSString stringWithFormat:@"test"];
	[str retain];
	[aryaddObject:str];
	NSLog(@”%@%d”,str,[str retainCount]);
	[str retain];
	[str release];
	[str release];
	NSLog(@”%@%d”,str,[str retainCount]);
	[aryremoveAllObjects];
	NSLog(@”%@%d”,str,[str retainCount]);

> * str的retainCount：创建+1，retain+1，加入数组自动+1 3
>  
> * retain+1，release-1，release-1 2
> 
> * 数组删除所有对象，所有数组内的对象自动-1 1

#### 213.内存管理的几条原则时什么?按照默认法则.那些关键字生成的对象需要手动释放?在和property结合的时候怎样有效的避免内存泄露?

> * 谁申请，谁释放
>  
> * 遵循Cocoa Touch的使用原则;
>  
> * 内存管理主要要避免“过早释放”和“内存泄漏”，对于“过早释放”需要注意@property设置特性时，一定要用对特性关键字，对于“内存泄漏”，一定要申请了要负责释放，要细心。
>  
> * 关键字alloc 或new 生成的对象需要手动释放;
>  
> * 设置正确的property属性，对于retain需要在合适的地方释放，

#### 214.MVC设计模式是什么？ 你还熟悉什么设计模式？

> * 设计模式：并不是一种新技术，而是一种编码经验，使用比如java中的接口，iphone中的协议，继承关系等基本手段，用比较成熟的逻辑去处理某一种类型的事情，总结为所谓设计模式。面向对象编程中，java已经归纳了23种设计模式。
>  
> * mvc设计模式 ：模型，视图，控制器，可以将整个应用程序在思想上分成三大块，对应是的数据的存储或处理，前台的显示，业务逻辑的控制。 Iphone本身的设计思想就是遵循mvc设计模式。其不属于23种设计模式范畴。
>  
> * 代理模式：代理模式给某一个对象提供一个代理对象，并由代理对象控制对源对象的引用.比如一个工厂生产了产品，并不想直接卖给用户，而是搞了很多代理商，用户可以直接找代理商买东西，代理商从工厂进货.常见的如QQ的自动回复就属于代理拦截，代理模式在iphone中得到广泛应用.
>  
> * 单例模式：说白了就是一个类不通过alloc方式创建对象，而是用一个静态方法返回这个类的对象。系统只需要拥有一个的全局对象，这样有利于我们协调系统整体的行为，比如想获得[UIApplication sharedApplication];任何地方调用都可以得到 UIApplication的对象，这个对象是全局唯一的。
>  
> * 观察者模式： 当一个物体发生变化时，会通知所有观察这个物体的观察者让其做出反应。实现起来无非就是把所有观察者的对象给这个物体，当这个物体的发生改变，就会调用遍历所有观察者的对象调用观察者的方法从而达到通知观察者的目的。

> * 工厂模式：

	public class Factory{
	public static Sample creator(int which){
	if (which==1)
	return new SampleA();
	else if (which==2)
	return new SampleB();
	}
	}

#### 215.浅复制和深复制的区别?

> * 浅层复制：只复制指向对象的指针，而不复制引用对象本身。
>  
> * 深层复制：复制引用对象本身。
>  
> * 意思就是说我有个A对象，复制一份后得到A_copy对象后，对于浅复制来说，A和A_copy指向的是同一个内存资源，复制的只不过是是一个指针，对象本身资源
>  
> * 还是只有一份，那如果我们对A_copy执行了修改操作,那么发现A引用的对象同样被修改，这其实违背了我们复制拷贝的一个思想。深复制就好理解了,内存中存在了两份独立对象本身。
>  
> * 用网上一哥们通俗的话将就是：
>  
>  浅复制好比你和你的影子，你完蛋，你的影子也完蛋
>  
>  深复制好比你和你的克隆人，你完蛋，你的克隆人还活着。

#### 225.类别的作用?继承和类别在实现中有何区别?

> * category 可以在不获悉，不改变原来代码的情况下往里面添加新的方法，只能添加，不能删除修改，并且如果类别和原来类中的方法产生名称冲突，则类别将覆盖原来的方法，因为类别具有更高的优先级。
>  
> * 类别主要有3个作用：
> 
> 1).将类的实现分散到多个不同文件或多个不同框架中。
> 
> 2).创建对私有方法的前向引用。
> 
> 3).向对象添加非正式协议。
> 
> * 继承可以增加，修改或者删除方法，并且可以增加属性。

#### 226.类别和类扩展的区别。

> * category和extensions的不同在于 后者可以添加属性，前者可以用关联对象添加属性。另外后者添加的方法是必须要实现的。 
> * extensions可以认为是一个私有的Category。

#### 227.oc中的协议和java中的接口概念有何不同?

> * OC中的代理有2层含义，官方定义为 formal和informal protocol。前者和Java接口一样。
>  
> * informal protocol中的方法属于设计模式考虑范畴，不是必须实现的，但是如果有实现，就会改变类的属性。
>  
> * 其实关于正式协议，类别和非正式协议我很早前学习的时候大致看过，也写在了学习教程里
>  
> * “非正式协议概念其实就是类别的另一种表达方式“这里有一些你可能希望实现的方法，你可以使用他们更好的完成工作”。
>  
> * 这个意思是，这些是可选的。比如我门要一个更好的方法，我们就会申明一个这样的类别去实现。然后你在后期可以直接使用这些更好的方法。
> 
> * 这么看，总觉得类别这玩意儿有点像协议的可选协议。”
>  
> * 现在来看，其实protocal已经开始对两者都统一和规范起来操作，因为资料中说“非正式协议使用interface修饰“，
>  
> * 现在我们看到协议中两个修饰词：“必须实现(@requied)”和“可选实现(@optional)”。

228. 什么是KVO和KVC?

> * KVC:键 – 值编码是一种间接访问对象的属性使用字符串来标识属性，而不是通过调用存取方法，直接或通过实例变量访问的机制。
>  
>  很多情况下可以简化程序代码。apple文档其实给了一个很好的例子。
>  
> * KVO:键值观察机制，他提供了观察某一属性变化的方法，极大的简化了代码。
>  
> * 具体用看到嗯哼用到过的一个地方是对于按钮点击变化状态的的监控。

>  **比如我自定义的一个button：**

	[self addObserver:self forKeyPath:@"highlighted" options:0 context:nil];
	#pragma mark KVO
	- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary *)change context:(void *)context
	{
	if ([keyPath isEqualToString:@"highlighted"] ) {
	[self setNeedsDisplay];
	}
	}


> 对于系统是根据keypath去取的到相应的值发生改变，理论上来说是和kvc机制的道理是一样的。
> 
> * 对于kvc机制如何通过key寻找到value：
> 
> “当通过KVC调用对象时，比如：[self valueForKey:@”someKey”]时，程序会自动试图通过几种不同的方式解析这个调用。首先查找对象是否带有 someKey 这个方法，如果没找到，会继续查找对象是否带有someKey这个实例变量(iVar)，如果还没有找到，程序会继续试图调用 -(id) valueForUndefinedKey:这个方法。如果这个方法还是没有被实现的话，程序会抛出一个NSUndefinedKeyException异常错误。
> 
> (cocoachina.com注：Key-Value Coding查找方法的时候，不仅仅会查找someKey这个方法，还会查找getsomeKey这个方法，前面加一个get，或者_someKey以及_getsomeKey这几种形式。同时，查找实例变量的时候也会不仅仅查找someKey这个变量，也会查找_someKey这个变量是否存在。)
> 
> 设计valueForUndefinedKey:方法的主要目的是当你使用-(id)valueForKey方法从对象中请求值时，对象能够在错误发生前，有最后的机会响应这个请求。这样做有很多好处，下面的两个例子说明了这样做的好处。“
> 
> 来至cocoa，这个说法应该挺有道理。
> 
> 因为我们知道butto确实存在一个highlighted实例变量.因此为何上面我们只是add一个相关的keypath就行了，
> 
> 可以按照kvc查找的逻辑理解，就说的过去了。

#### 229.代理的作用?

> * 代理的目的是改变或传递控制链。允许一个类在某些特定时刻通知到其他类，而不需要获取到那些类的指针。可以减少框架复杂度。
>  
> * 另外一点，代理可以理解为java中的回调监听机制的一种类似。

#### 230.我们说的oc是动态运行时语言是什么意思?

> * 多态。 主要是将数据类型的确定由编译时，推迟到了运行时。
> 
> * 这个问题其实浅涉及到两个概念，运行时和多态。
> 
> * 简单来说，运行时机制使我们直到运行时才去决定一个对象的类别，以及调用该类别对象指定方法。
> 
> * 多态：不同对象以自己的方式响应相同的消息的能力叫做多态。意思就是假设生物类(life)都用有一个相同的方法-eat;
> 
> * 那人类属于生物，猪也属于生物，都继承了life后，实现各自的eat，但是调用是我们只需调用各自的eat方法。
> 
> * 也就是不同的对象以自己的方式响应了相同的消息(响应了eat这个选择器)。
> 
> * 因此也可以说，运行时机制是多态的基础?~~~

#### 231.通知和协议的不同之处?

> * 协议有控制链(has-a)的关系，通知没有。
> 
> * 首先我一开始也不太明白，什么叫控制链(专业术语了~)。但是简单分析下通知和代理的行为模式，我们大致可以有自己的理解
> 
> * 简单来说，通知的话，它可以一对多，一条消息可以发送给多个消息接受者。
> 
> * 代理按我们的理解，到不是直接说不能一对多，比如我们知道的明星经济代理人，很多时候一个经济人负责好几个明星的事务。
> 
> * 只是对于不同明星间，代理的事物对象都是不一样的，一一对应，不可能说明天要处理A明星要一个发布会，代理人发出处理发布会的消息后，别称B的发布会了。但是通知就不一样，他只关心发出通知，而不关心多少接收到感兴趣要处理。
> 
> * 因此控制链(has-a从英语单词大致可以看出，单一拥有和可控制的对应关系。

#### 232.什么是推送消息?

> * 推送通知更是一种技术。
>  
> * 简单点就是客户端获取资源的一种手段。
>  
> * 普通情况下，都是客户端主动的pull。
>  
> * 推送则是服务器端主动push。 测试push的实现可以查看该博文。

#### 233.关于多态性

> * 多态，子类指针可以赋值给父类。
>  
> * 这个题目其实可以出到一切面向对象语言中，
>  
> * 因此关于多态，继承和封装基本最好都有个自我意识的理解，也并非一定要把书上资料上写的能背出来

#### 234. 对于单例的理解

>  *在objective-c中要实现一个单例类，至少需要做以下四个步骤：*
>  
> * 1).为单例对象实现一个静态实例，并初始化，然后设置成nil，
>  
> * 2).实现一个实例构造方法检查上面声明的静态实例是否为nil，如果是则新建并返回一个本类的实例，
>  
> * 3).重写allocWithZone方法，用来保证其他人直接使用alloc和init试图获得一个新实力的时候不产生一个新实例，
>  
> * 4).适当实现allocWitheZone，copyWithZone，release和autorelease。

#### 235.NSOperation queue?

> * 存放NSOperation的集合类。
>  
> * 操作和操作队列，基本可以看成java中的线程和线程池的概念。用于处理ios多线程开发的问题。
>  
> * 网上部分资料提到一点是，虽然是queue，但是却并不是带有队列的概念，放入的操作并非是按照严格的先进现出。
>  
> * 这边又有个疑点是，对于队列来说，先进先出的概念是Afunc添加进队列，Bfunc紧跟着也进入队列，Afunc先执行这个是必然的，
>  
> * 但是Bfunc是等Afunc完全操作完以后，B才开始启动并且执行，因此队列的概念离乱上有点违背了多线程处理这个概念。
>  
> * 但是转念一想其实可以参考银行的取票和叫号系统。
>  
> * 因此对于A比B先排队取票但是B率先执行完操作，我们亦然可以感性认为这还是一个队列。
>  
> * 但是后来看到一票关于这操作队列话题的文章，其中有一句提到
>  
> * “因为两个操作提交的时间间隔很近，线程池中的线程，谁先启动是不定的。”
>  
> * 瞬间觉得这个queue名字有点忽悠人了，还不如pool~
>  
> * 综合一点，我们知道他可以比较大的用处在于可以帮组多线程编程就好了。

#### 236.是否在一个视图控制器中嵌入两个tableview控制器?

> * 一个视图控制只提供了一个View视图，理论上一个tableViewController也不能放吧，
>  
> * 只能说可以嵌入一个tableview视图。当然，题目本身也有歧义，如果不是我们定性思维认为的UIViewController，而是宏观的表示视图控制者，那我们倒是可以把其看成一个视图控制者，它可以控制多个视图控制器，比如TabbarController那样的感觉。

#### 237.一个tableView是否可以关联两个不同的数据源?你会怎么处理?

> * 首先我们从代码来看，数据源如何关联上的，其实是在数据源关联的代理方法里实现的。
>  
> * 因此我们并不关心如何去关联他，他怎么关联上，方法只是让我返回根据自己的需要去设置如相关的数据源。
>  
> * 因此，我觉得可以设置多个数据源啊，但是有个问题是，你这是想干嘛呢?想让列表如何显示，不同的数据源分区块显示?

#### 238.什么时候使用NSMutableArray，什么时候使用NSArray?

> * 当数组在程序运行时，需要不断变化的，使用NSMutableArray，当数组在初始化后，便不再改变的，使用NSArray。需要指出的是，使用NSArray只表明的是该数组在运行时不发生改变，即不能往NSAarry的数组里新增和删除元素，但不表明其数组內的元素的内容不能发生改变。NSArray是线程安全的，NSMutableArray不是线程安全的，多线程使用到NSMutableArray需要注意。

#### 239.给出委托方法的实例，并且说出UITableVIew的Data Source方法

> * CocoaTouch框架中用到了大量委托，其中UITableViewDelegate就是委托机制的典型应用，是一个典型的使用委托来实现适配器模式，其中UITableViewDelegate协议是目标，tableview是适配器，实现UITableViewDelegate协议，并将自身设置为talbeview的delegate的对象，是被适配器，一般情况下该对象是UITableViewController。

	UITableVIew的Data Source方法有- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section;
	
	-(UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath;

#### 240.在应用中可以创建多少autorelease对象，是否有限制?

> * 无

#### 241.如果我们不创建内存池，是否有内存池提供给我们?

> * 界面线程维护着自己的内存池，用户自己创建的数据线程，则需要创建该线程的内存池

#### 242.什么时候需要在程序中创建内存池?

> * 用户自己创建的数据线程，则需要创建该线程的内存池

#### 243.类NSObject的那些方法经常被使用?

> * NSObject是Objetive-C的基类，其由NSObject类及一系列协议构成。
>  
> * 其中类方法alloc、class、 description 对象方法init、dealloc、– performSelector:withObject:afterDelay:等经常被使用

#### 244.什么是简便构造方法?

> * 简便构造方法一般由CocoaTouch框架提供，如NSNumber的 + numberWithBool: + numberWithChar: + numberWithDouble: + numberWithFloat: + numberWithInt:
>  
> * Foundation下大部分类均有简便构造方法，我们可以通过简便构造方法，获得系统给我们创建好的对象，并且不需要手动释放。

#### 245.如何使用Xcode设计通用应用?

> * 使用MVC模式设计应用，其中Model层完成脱离界面，即在Model层，其是可运行在任何设备上，在controller层，根据iPhone与iPad(独有UISplitViewController)的不同特点选择不同的viewController对象。在View层，可根据现实要求，来设计，其中以xib文件设计时，其设置其为universal。

#### 246.UIView的动画效果有那些?

> * 有很多，如 UIViewAnimationOptionCurveEaseInOut UIViewAnimationOptionCurveEaseIn UIViewAnimationOptionCurveEaseOut UIViewAnimationOptionTransitionFlipFromLeft UIViewAnimationOptionTransitionFlipFromRight UIViewAnimationOptionTransitionCurlUpUIViewAnimationOptionTransitionCurlDown

#### 247. 在iPhone应用中如何保存数据?

> * 有以下几种保存机制：
>  
> * 1).通过web服务，保存在服务器上
>  
> * 2).通过NSCoder固化机制，将对象保存在文件中
> 
> * 3).通过SQlite或CoreData保存在文件数据库中

#### 248. 什么是coredata?

> * coredata是苹果提供一套数据保存框架，其基于SQlite

#### 249.什么是NSManagedObject模型?

> * NSManagedObject是NSObject的子类 ，也是coredata的重要组成部分，它是一个通用的类,实现了core data 模型层所需的基本功能，用户可通过子类化NSManagedObject，建立自己的数据模型。

#### 250.什么是NSManagedobjectContext?

> * NSManagedobjectContext对象负责应用和数据库之间的交互。

#### 251. 谈谈对Block 的理解?并写出一个使用Block执行UIVew动画?

> * Block是可以获取其他函数局部变量的匿名函数，其不但方便开发，并且可以大幅提高应用的执行效率(多核心CPU可直接处理Block指令)

	[UIView transitionWithView:self.view
	duration:0.2
	options:UIViewAnimationOptionTransitionFlipFromLeft
	animations:^{ [[blueViewController view] removeFromSuperview]; [[self view] insertSubview:yellowViewController.view atIndex:0]; }
	completion:NULL];

#### 252.写出上面代码的Block的定义。

	typedef void(^animations) (void);
	typedef void(^completion) (BOOL finished);






















































































































































































