---
title: Flutter中widget的生命周期
categories:   
 - Flutter
 - Widget生命周期
tags: 
- flutter
- 源码
---

# Flutter页面生命周期

* Flutter 官网 : [https://flutter.dev/]()
* Flutter 插件下载地址 : [https://pub.dev/packages]()
* Flutter 开发文档 : [https://flutter.cn/docs]() 
* 官方 GitHub 地址 : [https://github.com/flutter]()
* Flutter 中文社区 : [https://flutter.cn/]()
* Flutter 实用教程 : [https://flutter.cn/docs/cookbook]()
* Flutter CodeLab : [https://codelabs.flutter-io.cn/]()
* Dart 中文文档 : [https://dart.cn/]()
* Dart 开发者官网 : [https://api.dart.dev/]()
* Flutter 中文网 : [https://flutterchina.club/ ](), [http://flutter.axuer.com/docs/]()
* Flutter 相关问题 : [https://flutterchina.club/faq/]() 

### 1.Flutter中的两种基础组件：

* StatefulWidget 组件  
* StatelessWidget 组件 

2.先看下Stateless的源码：

	abstract class StatelessWidget extends Widget {
	  /// Initializes [key] for subclasses.
	  const StatelessWidget({ Key key }) : super(key: key);
	  
	  @override
	  StatelessElement createElement() => StatelessElement(this);
		
	  @protected
	  Widget build(BuildContext context);
	}

可以看出内部只有两个生命周期的函数：

***createElement():***  

***build():***

### 3.再看下StatefulWidget的源码：

StatefulWidget 组件生命周期分为三组 :

① 初始化期 : createState , initState ;

② 更新期 : didChangeDependencies , build , didUpdateWidget ;

③ 销毁期 : deactivate , dispose ;

#### createState：

createState 函数 :

	@protected
	@factory
	State createState();

* 所处时期 : 初始化期的生命周期函数
* 调用时机 : 创建 StatefulWidget 之后调用的第一个方法 ;
* 抽象方法 : 该方法是抽象方法 , 必须覆盖重写该方法 ;

#### initState：

initState 函数 :
	
	@protected
	@mustCallSuper
	void initState() {
	assert(_debugLifecycleState == _StateLifecycle.created);
	}

* 所处时期 : 初始化期的生命周期函数
* 调用时机 : 该方法是创建 Widget 组件时除构造方法之外的第一个方法 ,
* 对应方法 : 对应 Android 中的 onCreate 方法 ; 对应 iOS 中的 viewDidLoad 方法 ;
* 常用用法 : 在该方法中执行一些初始化操作 ;

#### didChangeDependencies：

didChangeDependencies 函数 :

	@protected
	@mustCallSuper
	void didChangeDependencies() { }

* 所处时期 : 更新期的生命周期函数 ;
* 调用时机 : ① 创建 Widget 组件时 , 调用完 initState 方法后 , 调用该方法 ; ② InheritedWidget 相关 ( 暂不涉及 ) ;

#### build：

build 函数 :

	@protected
	Widget build(BuildContext context);

* 所处时期 : 更新期的生命周期函数 ;
* 调用时机 : ① 调用完 didChangeDependencies 方法后调用该方法 ; ② 调用 setState 方法之后 , 该方法也会被调用 ;
* 方法作用 : 页面每次渲染时都会调用该方法 ;

#### didUpdateWidget：

didUpdateWidget 函数 :

	@mustCallSuper
	@protected
	void didUpdateWidget(covariant T oldWidget) { }

* 所处时期 : 更新期的生命周期函数 ;
* 调用时机 : 该生命周期方法不经常调用 , 只有在父容器组件重绘时才调用该方法 ;
* 方法机制 : 传入的 oldWidget 参数是旧的组件信息 , 将当前的 Widget 与旧的 Widget 进行对比 , 如果不相等 , 便执行额外操作 ;

#### deactivate：

deactivate 函数 :

	@protected
	@mustCallSuper
	void deactivate() { }

* 所处时期 : 销毁期的生命周期函数 ;
* 调用时机 : 该生命周期方法不经常调用 , 只有在组件被移除时才调用 ;

#### dispose：

dispose 函数：

	@protected
		@mustCallSuper
		void dispose() {
		assert(_debugLifecycleState == _StateLifecycle.ready);
		assert(() {
		_debugLifecycleState = _StateLifecycle.defunct;
		return true;
		}());
		}

* 所处时期 : 销毁期的生命周期函数 ;
* 调用时机 : 组件被销毁时调用 , 要在该方法中进行资源的释放与销毁操作 ;

ToDo：所以页面生命周期的流程图是：




































