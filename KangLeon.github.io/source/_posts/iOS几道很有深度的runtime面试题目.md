---
title: iOS几道runtime面试题目
categories:   
 - iOS
 - 面试、笔试
tags: 
- iOS
- Job Interview
- Written examination
---

### 1.在OC中初始化对象时，`[[class_name alloc] init]`和`[class_name new]`这两种方式有什么区别？
源码：

**alloc的实现与new实现的对比：**

	+(id)alloc{
		return _objc_rootAlloc(self);
	}
	id_objc_rootAlloc(Class cls){
		return  callAlloc(cls,false.true);
	}
	+(id)new{
		return [callAlloc(self,false) init];
	}

**init方法的实现：**

	-(id)init{
		return _objc_rootInit(self);
	}
	id _obj_rootInit(id obj){
		return obj;
	}
	
> 事实上`alloc+init=new`，在内部实现中，参数zone已经被忽略掉了，最终都是调用class_createInstance。即`alloc+init`和`new`两种方式没有本质区别，但是如果想要initWithXXX这种形式初始化对象，那么使用alloc+init方式。一般new只是在开发人员在不熟悉OC的情况下使用的一个辅助方法。

### 2.Runtime如何通过selector找到对应的IMP地址？（需要分别考虑类方法和实例方法）

**三种情况：**

* `objc_msgSend`
* `methodForSelector`
* `method_getImplementation`

##### 1.objc_msgSend:

![](/uploads/images/iOS几道很有深度的runtime面试题目/objc_msgSend.png)

##### 2.methodForSelector:

![](/uploads/images/iOS几道很有深度的runtime面试题目/methodForSelector.png)

##### 3.method_getImplementation

![](/uploads/images/iOS几道很有深度的runtime面试题目/method_getImplementation.png)

* selecotr实质上是一个方法名name，遍历方法列表可查询对应的method，method中包含方法名name以及方法实现imp
* 实例的方法在其对应的类方法列表中查询，通过isa可获取其类
* 类的方法在其对应的元类方法列表中查询，通过isa或者getMeta

### 3.能否向编译后得到的类中增加实例变量？能否向运行时创建的类中添加实例变量？为什么？

**答案：**不可以向编译后的类中增加实例变量，可以向运行时创建的类中添加实例比纳凉。

* 类编译时只读结构体`class_ro_t`就会被确定，运行时不可修改
* ro结构体中的`ivar_list_t`也是不可修改的，并且`instanceSize`决定了创建对象时需要的空间大小
* 在`objc_allocateClassPair`和`objc_registerClassPair`之间调用`class_addIvar`

### 4.给类添加一个属性后，在类结构体里那些元素会发生变化？

##### 首先对于`@property()`这种方法,以下是发生变化的地方：

* `class_ro_t->isntanceSize`:实例大小
* `class_ro_t->ivar_list_t`:变量列表
* `class_rw_t->method_list_t`:方法列表
* `class_rw_t->property_list_t`:属性列表

##### 然而对于`class_addProperty`这种方法，以下是发生变化的地方：

* `class_rw_t->property_list_t`:属性列表

### 5.`_objc_msgForward`函数是做什么的？直接调用它将会发生什么？

![](/uploads/images/iOS几道很有深度的runtime面试题目/_objc_msgForward.png)

* `_objc_msgForward`是一个函数地址即IMP，用于消息转发
* 当一个对象未执行未实现的方法时，会调用此方法
* `resolveClassMethod`和`resolveInstanceMethod`不属于消息转发流程，是属于消息查找过程中的，如果IMP就要开始动态解析，动态解析不成功后会调用消息转发。
* 直接调用`_objc_msgForward `会崩溃的，因为系统会默认你没有找到这个方法的IMP，直接进行消息转发流程，但是你后面的方法都没有实现，所以就会crash

### 6.使用`Runtime Associate`方法关联的对象，需要在主对象delloc的时候释放么？

##### 对象的销毁步骤：

* 主对象引用计数为0时，调用`_release`
* 沿继承链调用父类的`-dealloc`
* 直到调用`NSObject`的`-dealloc`
* 相关函数：`dealloc->_objc_rootDealloc->rootDealloc->object_despose->objc_destructInstance`

##### 对象销毁的内容：

* 释放实例变量：`object_cxxDestruct`
* 移除关联属性：`_object_remove_assocations`
* 将弱引用置为nil：`clearDeallocating`

![](/uploads/images/iOS几道很有深度的runtime面试题目/属性关联.png)

* 属性关联的对象不需要在主对象`dealloc`中释放
* 它们会在被`NSObject-dealloc`调用的`object_dispose()`方法中释放
* 释放时，如果是强引用retain，则release，不是retain的话，不会管

### 7.`_runtime`如何实现weak属性？那么runtime如何实现weak变量的自动置nil?

> runtime源码文件中的：`objc-weak.h`，该文件里面有很多弱引用的相关方法：

* 添加弱引用：`weak_register_no_lock`
* 移除一个弱引用：`weak_unregister_no_lock`
* 指定对象是否存在弱引用：`weak_is_registered_no_lock`
* 清楚指定对象所有弱引用：`weak_clear_no_lock`

![](/uploads/images/iOS几道很有深度的runtime面试题目/SideTables.png)

> weak的实现：

* 执行`__weak ClassA *objB = objA`
* 会调用`NSObject`的`objc_initWeak`方法

> weak自动nil的实现：

* 首先执行`clearDeallocation`方法
* 再次调用`objc-weak.h`文件中的`weak_clear_no_lock`方法

> 所以

* 当一个对象有弱引用时，会封装成一个结构体，将其放入一个数组中，并根据该对象的地址作为标识
* 当该对象的引用计数为0时，会以其地址为标识遍历搜索数组，把指向该对象所有指针都置为nil

### 8.以下代码会输出什么？

	@implementation Son : Father
	-(id)init{
		self = [super init];
		if(self){
			NSLog(@"%@",NSStringFromClass([self class]));
			NSLog(@"%@",NSStringFromClass([super class]));
		}
		return self;
	}
	@end

答案：都输出son

![](/uploads/images/iOS几道很有深度的runtime面试题目/方法查找流程.png)

>  NSObject中class实现

		-(Class)class{
			return object_getClass(self);
		}
		Class object_getClass(id obj){
			if(obj) return obj->getIsa();
			else return Nil;
		}

> [super class]方法的内部实现

		struct objc_super objcSuper = {
			self,
			class_getSuperClass([self class]),
		};
		objc_msgSendSuper(&objcSuper,@selector(class));

> 总结：

* Father和Son都没有实现Class方法时，二者唯一区别就是[super class]比[self class]快了一步

### 9.下面代码会输出什么？

		BOOL res1=[(id)[NSObject class] isKindOfClass:[NSObject class]];
		BOOL res2=[(id)[NSObject class] isMemberOfClass:[NSObject class]];
		BOOL res3=[(id)[Sark class] isKindOfClass:[Sark class]];
		BOOL res4=[(id)[Sark class] isMemberOfClass:[Sark class]];

答案：1、0、0、0

> `isKindOfClass:`和`isMemberOfClass:`源码实现

		+(BOOL)iskindOfClass:(Class)cls{
			for(Class tcls=object_getClass((id)self);tcls;tcls=tcls->superclass){
				if(tcls==cls) return YES;
			}
			return NO;
		}
		+(BOOL)isMemberOfClass:(Class)cls{
			return object_getClass((id)self) == cls;
		}

> 总结：

* `isKindOfClass:`判断该类对象所属的类，其继承链是否包含传入的class
* `isMemberOfClass:`判断该类对象所属的类是否等于传入的class

### 10.下面代码会？Compile Error/Runtime Crash/NSLog...?

		@interface NSObejct (Sark)
		+(void)foo;
		@end
		@implementation NSObject(Sark)
		-(void)foo{
			NSLog(@"IMP: -[NSObject(Sark) foo]");
		}
		@end
		
		//调用
		[NSObject foo];
		[[NSObject new] performSelector: @selector(foo)];















