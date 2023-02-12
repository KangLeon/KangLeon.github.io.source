---
title: RXSwift学习笔记
categories:   
 - iOS
 - RXSwift
tags: 
- iOS
- RXSwift
---

## 为什么使用RXSwift？

* 复合 - Rx 就是复合的代名词
* 复用 - 因为它易复合
* 清晰 - 因为声明都是不可变更的
* 易用 - 因为它抽象的了异步编程，使我们统一了代码风格
* 稳定 - 因为 Rx 是完全通过单元测试的

### 1.代替Target Action

	button.rx.tap
    .subscribe(onNext: {
        print("button Tapped")
    })
    .disposed(by: disposeBag)
    
### 2.代替代理

	scrollView.rx.contentOffset
	            .subscribe(onNext: { contentOffset in
	                print("contentOffset: \(contentOffset)")
	            })
	            .disposed(by: disposeBag)
	    }
	}

### 3.代替闭包回调

	URLSession.shared.rx.data(request: URLRequest(url: url))
	    .subscribe(onNext: { data in
	        print("Data Task Success with count: \(data.count)")
	    }, onError: { error in
	        print("Data Task Error: \(error)")
	    })
	    .disposed(by: disposeBag)
	    
### 4.代替通知

	NotificationCenter.default.rx
	        .notification(.UIApplicationWillEnterForeground)
	        .subscribe(onNext: { (notification) in
	            print("Application Will Enter Foreground")
	        })
	        .disposed(by: disposeBag)

### 5.对于多个任务之间有依赖关系的

例如，先通过用户名密码取得 Token 然后通过 Token 取得用户信息，

#### 传统实现方法：

	/// 用回调的方式封装接口
	enum API {
	
	    /// 通过用户名密码取得一个 token
	    static func token(username: String, password: String,
	        success: (String) -> Void,
	        failure: (Error) -> Void) { ... }
	        
		/// 通过 token 取得用户信息
		    static func userinfo(token: String,
		        success: (UserInfo) -> Void,
		        failure: (Error) -> Void) { ... }
	}

	/// 通过用户名和密码获取用户信息
	API.token(username: "beeth0ven", password: "987654321",
	    success: { token in
	        API.userInfo(token: token,
	            success: { userInfo in
	                print("获取用户信息成功: \(userInfo)")
	            },
	            failure: { error in
	                print("获取用户信息失败: \(error)")
	        })
	    },
	    failure: { error in
	        print("获取用户信息失败: \(error)")
	})
	
#### 通过Rx来实现：

	/// 用 Rx 封装接口
	enum API {

	    /// 通过用户名密码取得一个 token
	    static func token(username: String, password: String) -> Observable<String> { ... }
	
	    /// 通过 token 取得用户信息
	    static func userInfo(token: String) -> Observable<UserInfo> { ... }
	}
	
	“/// 通过用户名和密码获取用户信息
	API.token(username: "beeth0ven", password: "987654321")
	    .flatMapLatest(API.userInfo)
	    .subscribe(onNext: { userInfo in
	        print("获取用户信息成功: \(userInfo)")
	    }, onError: { error in
	        print("获取用户信息失败: \(error)")
	    })
	    .disposed(by: disposeBag)
	    
### 6.等待多个并发任务完成后处理

例如，需要将两个网络请求合并成一个，

#### 通过 Rx 来实现：

	/// 用 Rx 封装接口
	enum API {

	    /// 取得老师的详细信息
	    static func teacher(teacherId: Int) -> Observable<Teacher> { ... }
	
	    /// 取得老师的评论
	    static func teacherComments(teacherId: Int) -> Observable<[Comment]> { ... }
	}
	
	“/// 同时取得老师信息和老师评论
	Observable.zip(
	      API.teacher(teacherId: teacherId),
	      API.teacherComments(teacherId: teacherId)
	    ).subscribe(onNext: { (teacher, comments) in
	        print("获取老师信息成功: \(teacher)")
	        print("获取老师评论成功: \(comments.count) 条")
	    }, onError: { error in
	        print("获取老师信息或评论失败: \(error)")
	    })
	    .disposed(by: disposeBag)
	
### 7.以下是经典登录Demo：

	override func viewDidLoad() {
	    super.viewDidLoad()
	
	    usernameValidOutlet.text = "Username has to be at least \(minimalUsernameLength) characters"
	    passwordValidOutlet.text = "Password has to be at least \(minimalPasswordLength) characters"

		 let usernameValid = usernameOutlet.rx.text.orEmpty
	        .map { $0.count >= minimalUsernameLength }
	        .share(replay: 1)
	
	    let passwordValid = passwordOutlet.rx.text.orEmpty
	        .map { $0.count >= minimalPasswordLength }
	        .share(replay: 1)
	
	    let everythingValid = Observable.combineLatest(
	          usernameValid,
	          passwordValid
	        ) { $0 && $1 }
	        .share(replay: 1)
	    usernameValid
		    .bind(to: passwordOutlet.rx.isEnabled)
		    .disposed(by: disposeBag)
			
		usernameValid
		    .bind(to: usernameValidOutlet.rx.isHidden)
		    .disposed(by: disposeBag)
			
		passwordValid
		    .bind(to: passwordValidOutlet.rx.isHidden)
		    .disposed(by: disposeBag)
			
		everythingValid
		    .bind(to: doSomethingOutlet.rx.isEnabled)
		    .disposed(by: disposeBag)
			
		doSomethingOutlet.rx.tap.subscribe(onNext: { [weak self] in self?.showAlert() })
        .disposed(by: disposeBag)
	}
	
	func showAlert() {
	    let alertView = UIAlertView(
	        title: "RxExample",
	        message: "This is wonderful",
	        delegate: nil,
	        cancelButtonTitle: "OK"
	    )
	
	    alertView.show()
	}
	
	
> 注意：share(replay: 1) 是用来做什么的？
> 
> 我们用 usernameValid 来控制用户名提示语是否隐藏以及密码输入框是否可用。shareReplay 就是让他们共享这一个源，而不是为他们单独创建新的源。这样可以减少不必要的开支。
> 
> disposed(by: disposeBag) 是用来做什么的？
> 
> 和我们所熟悉的对象一样，每一个绑定也是有生命周期的。并且这个绑定是可以被清除的。disposed(by: disposeBag)就是将绑定的生命周期交给 disposeBag 来管理。当 disposeBag 被释放的时候，那么里面尚未清除的绑定也就被清除了。
	
### 8.函数式编程

函数式编程是种编程范式，它需要我们将函数作为参数传递，或者作为返回值返还。我们可以通过组合不同的函数来得到想要的结果。

函数式编程的优点：

* 灵活
* 高复用
* 简洁
* 易维护
* 适应各种需求变化

### 9.函数响应式编程

我们通过不同的构建函数，来创建所需要的数据序列。最后通过适当的方式来响应这个序列。这就是函数响应式编程。
	
### 10.数据绑定（订阅）

在 RxSwift 里有一个比较重要的概念就是数据绑定（订阅）。就是指将可监听序列绑定到观察者上：

#### 我们对比一下这两段代码：

	let image: UIImage = UIImage(named: ...)
	imageView.image = image
	
	let image: Observable<UIImage> = ...
	image.bind(to: imageView.rx.image)
	
第一段代码我们非常熟悉，它就是将一个单独的图片设置到imageView上。	
第二段代码则是将一个图片序列 “同步” 到imageView上。这个序列里面的图片可以是异步产生的。这里定义的 image 就是上图中蓝色部分（可监听序列），imageView.rx.image就是上图中橙色部分（观察者）。而这种 “同步机制” 就是数据绑定（订阅）。	
### 11.Observable - 可监听序列
	
“Observable 可以用于描述元素异步产生的序列。”

#### 如何创建序列?

实际上，框架已经帮我们创建好了许多常用的序列。例如：button的点击，textField的当前文本，switch的开关状态，slider的当前数值等等。

	let numbers: Observable<Int> = Observable.create { observer -> Disposable in
	
	    observer.onNext(0)
	    observer.onNext(1)
	    observer.onNext(2)
	    observer.onNext(3)
	    observer.onNext(4)
	    observer.onNext(5)
	    observer.onNext(6)
	    observer.onNext(7)
	    observer.onNext(8)
	    observer.onNext(9)
	    observer.onCompleted()
	
	    return Disposables.create()
	}

创建序列最直接的方法就是调用 Observable.create，然后在构建函数里面描述元素的产生过程。 observer.onNext(0) 就代表产生了一个元素，他的值是 0。后面又产生了 9 个元素分别是 1, 2, ... 8, 9 。最后，用 observer.onCompleted() 表示元素已经全部产生，没有更多元素了。

你可以用这种方式来封装功能组件，例如，闭包回调：

	typealias JSON = Any
	
	let json: Observable<JSON> = Observable.create { (observer) -> Disposable in

    let task = 
	URLSession.shared.dataTask(with: ...) { data, _, error in

        guard error == nil else {
            observer.onError(error!)
            return
        }

        guard let data = data,
            let jsonObject = try? JSONSerialization.jsonObject(with: data, options: .mutableLeaves)
            else {
            observer.onError(DataError.cantParseJSON)
            return
        }

        observer.onNext(jsonObject)
        observer.onCompleted()
	 }

    task.resume()

    return Disposables.create { task.cancel() }
	}

在闭包回调中，如果任务失败，就调用 observer.onError(error!)。如果获取到目标元素，就调用 observer.onNext(jsonObject)。由于我们的这个序列只有一个元素，所以在成功获取到元素后，就直接调用 observer.onCompleted() 来表示任务结束。最后 Disposables.create { task.cancel() } 说明如果数据绑定被清除（订阅被取消）的话，就取消网络请求。

这样一来我们就将传统的闭包回调转换成序列了。然后可以用 subscribe 方法来响应这个请求的结果：

	json
	    .subscribe(onNext: { json in
	        print("取得 json 成功: \(json)")
	    }, onError: { error in
	        print("取得 json 失败 Error: \(error.localizedDescription)")
	    }, onCompleted: {
	        print("取得 json 任务成功完成")
	    })
	    .disposed(by: disposeBag) 

这里subscribe后面的onNext,onError, onCompleted 分别响应我们创建 json 时，构建函数里面的onNext,onError, onCompleted 事件。我们称这些事件为 *Event*: 

### Event - 事件

	public enum Event<Element> {
	    case next(Element)
	    case error(Swift.Error)
	    case completed
	}

* next - 序列产生了一个新的元素
* error - 创建序列时产生了一个错误，导致序列终止
* completed - 序列的所有元素都已经成功产生，整个序列已经完成
你可以合理的利用这些 Event 来实现业务逻辑。

### 特征序列

在 RxSwift 里面 Observable 也存在一些特征序列，这些特征序列可以帮助我们更准确的描述序列。并且它们还可以给我们提供语法糖，让我们能够用更加优雅的方式书写代码，他们分别是：

* Single
* Completable
* Maybe
* Driver
* Signal
* ControlEvent

### Single

Single 是 Observable 的另外一个版本。不像 Observable 可以发出多个元素，它要么只能发出一个元素，要么产生一个 error 事件。

发出一个元素，或一个 error 事件
不会共享附加作用
一个比较常见的例子就是执行 HTTP 请求，然后返回一个应答或错误。不过你也可以用 Single 来描述任何只有一个元素的序列。

#### 如何创建 Single?

创建 Single 和创建 Observable 非常相似：
		
		func getRepo(_ repo: String) -> Single<[String: Any]> {
		
		return Single<[String: Any]>.create { single in
		
		let url = URL(string: "https://api.github.com/repos/\(repo)")!
		    let task = URLSession.shared.dataTask(with: url) {
		        data, _, error in
		
		    if let error = error {
		        single(.error(error))
		        return
		    }
		
		    guard let data = data,
		          let json = try? JSONSerialization.jsonObject(with: data, options: .mutableLeaves),
		          let result = json as? [String: Any] else {
		        single(.error(DataError.cantParseJSON))
		        return
		    }
		
			single(.success(result))
		}
		
		task.resume()
		
		return Disposables.create { task.cancel() }
	}
	}

之后，你可以这样使用 Single：

	getRepo("ReactiveX/RxSwift")
	    .subscribe(onSuccess: { json in
	        print("JSON: ", json)
	    }, onError: { error in
	        print("Error: ", error)
	    })
	    .disposed(by: disposeBag)

订阅提供一个 SingleEvent 的枚举：

public enum SingleEvent<Element> {
    case success(Element)
    case error(Swift.Error)
}

* success - 产生一个单独的元素
* error - 产生一个错误

你同样可以对 Observable 调用 .asSingle() 方法，将它转换为 Single。

















































	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	