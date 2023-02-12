---
title: A good application for ReactiveCocoa and MVVM
categories:   
 - iOS
 - RAC+MVVM
tags: 
- iOS
- RAC+MVVM
---

Description：

* 标题是英文的，只是在为托福做准备，所以希望在任何可以的地方练习一下英文的说和写。
* 本篇不再介绍基础的MVVM和RAC知识，所以在看本篇文章时，请确定你已经读了上一篇关于RAC+MVVM的文章，或者你已经对MVVM和RAC已经有很多的了解，你也可以直接阅读。
* 请带着批判的眼光来审视这篇文章，因为没有什么是完美的，假如可以催生出你更多的想法，那我花时间来写这篇文章也是很有价值的，

## 使用MVVM和RAC会解决你什么问题？

这个要从结果来看：

### 代码更加“紧密”：

我的意思是说，你可以利用类似**rac_signalForControlEvents：**这样的方法将按钮或者其他控件的响应事件就写在**subscribeNext**：的block中，像下面这样：

	-(LeftImageButton *)subjectHomework_button{
    if (!_subjectHomework_button) {
        _subjectHomework_button=[[LeftImageButton alloc] init];
        _subjectHomework_button.left_imageView.image=[UIImage imageNamed:@"项目制学习作业"];
        _subjectHomework_button.right_label.text=@"项目制学习作业布置";
        _subjectHomework_button.backgroundColor=fifty_nine_BACKGROUND_COLOR;
        [[_subjectHomework_button rac_signalForControlEvents:UIControlEventTouchUpInside] subscribeNext:^(__kindof UIControl * _Nullable x) {
            SNSubjectHomeworkViewController *subjectHomework_VC=[[SNSubjectHomeworkViewController alloc] init];
            [self.navigationController pushViewController:subjectHomework_VC animated:true];
        }];
    }
    return _subjectHomework_button;
	}

按钮的点击事件写在了懒加载的部分，这样的代码在维护起来很便利，否则用addTarget: action:forControlEvents:实现点击事件的代码中，其他方法的点击事件也有很多，很杂，m文件会充斥着大量的代码，你不断的command+c和command+f，你会被一个.m文件就搞得晕头转向。
还有像下面这样将通知的实现也写在监听通知信号的订阅block中：

	[[[NSNotificationCenter defaultCenter] rac_addObserverForName:GOT_INFORMATION_NOTI object:nil] subscribeNext:^(NSNotification * _Nullable x) {
        [self setHeadImage];
    }];

### VC真的瘦下来了：

对于一个处女座来说，在用了MVVM和RAC后我已经落下了几滴感动的眼泪。曾经一个燥热的午后，我在仿写QQ音乐App，为了设计那么美妙操作体验，我的VC不知不觉就1000+行，后来想要再更改操作逻辑时，简直是噩梦，除了上面所说的方法太多，跳来跳去，脑子就晕了。还有代码的分布没有一个规矩和风格，这在代码量少的时候无关紧要，但是在代码量多的时候，就会导致维护或更改时太痛苦了。

所以那个午后我终于明白了使VC瘦下来的必要性，同时也必须要形成一套编程风格。后来我的代码是这样组织的：

	#pragma mark - 初始化UI

	#pragma mark - 懒加载

	#pragma mark - 代理

	#pragma mark - target action

	#pragma mark - Maonry

	#pragma mark - other 只有本页面会使用的工具方法

这是一个基本的组织方法，把对应的代码都组织在特定的方法组中，这样不仅便于自己开发，还对后来维护你项目的人也很友好。还有更好的一点，有时候出现了crash，在控制台的提示信息中你就可以知道哪一个部分出了问题，找到对应的方法组修改即可。

回到正题，VC真的瘦下来了，Model、ViewModel、View各司其职，HomeVC一般是最容易堆积代码的地方，但是在MVVM下，我的每一个VC基本都控制在500行以下，她真正的瘦下来了。如果减肥有这么简单就好了。

好的，下面会以邮箱功能做一个简单的阐述，这只是一个站内信的邮箱功能，没有很多代码。

在MVVM下，我是这样设计View层的，在SNTeacherMailViewController中有一个tableView用来显示邮箱列表：

	@property(nonatomic,strong)UITableView *tableView;


懒加载：


	-(UITableView *)tableView{
    if (!_tableView) {
        _tableView=[[UITableView alloc] init];
        _tableView.delegate=self;
        _tableView.dataSource=self;
        _tableView.layer.cornerRadius=20.0;
        _tableView.backgroundColor=[UIColor whiteColor];
        _tableView.separatorInset=UIEdgeInsetsMake(0,SNTrueHeight(40), 0, SNTrueHeight(40));//top left bottom right 左右边距相同
        _tableView.separatorStyle=UITableViewCellSeparatorStyleSingleLine;
        
        [_tableView registerClass:[MailTableViewCell class] forCellReuseIdentifier:teacher_mail_cell_reuse];
    }
    return _tableView;
	}

下面要实现tableView的数据源和代理方法，ok，但是数据从哪里拿？VC在MVVM下已经被划分到View层，所以在SNTeacherMailViewController中不会再写发送网络请求的代码，而ViewModel是呈现给View数据的一个好帮手，网络请求将会在ViewModel中实现，并通过把ViewModel当作VC的一个属性来让VC获得数据。所以在实现tabeView的数据源和代理方法前，先来看看ViewModel，这是TeacherMailViewModel的头文件，

	#import <Foundation/Foundation.h>

	NS_ASSUME_NONNULL_BEGIN

	@interface TeacherMailViewModel : NSObject
	
	@property(nonatomic,strong,readonly)RACCommand *command;
	@property(nonatomic,strong,readonly)NSArray *mailArray;
	/**
	 获得邮箱数据
	 */
	-(void)getMailInformations;

	@end

他需要给SNTeacherMailViewController提供数据，所以他通过暴露mailArray属性来给SNTeacherMailViewController的tableView提供数据，其他部分你应当好好看一看，我还又一个方法：

	-(void)getMailInformations;

这个方法不会直接在VC中调用获得数据的，注意到

	@property(nonatomic,strong,readonly)RACCommand *command;
	
了吗，这个就是发送网络请求的东西，所以getMailInformations：方法的实现将会是这样子：

	-(void)getMailInformations{
    @weakify(self);
    self.command = [[RACCommand alloc] initWithSignalBlock:^RACSignal *(id input) {
        @strongify(self);
        return [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {
            @strongify(self);
            
            NSMutableDictionary *header=[NSMutableDictionary dictionaryWithObject:[[UserInfoHandler sharedUserInfoHandler] userToken] forKey:@"token"];
            NSMutableDictionary *body=[NSMutableDictionary dictionaryWithObject:@"nothing" forKey:@"nothing"];
            
            [[SNAFNetManager sharedManager] requestWithURLString:API_URL_messageTraining header:header parameters:body subModel:[TeacherMailModel class] success:^(BasicResponseModel * _Nonnull successData) {
                self.mailArray=successData.data;
                //发送订阅信号
                [subscriber sendNext:@""];
                //发送结束信号
                [subscriber sendCompleted];
            } failure:^(BasicResponseModel * _Nonnull failureData) {
                
            } progressPercentBlock:^(float percent) {
                
            }];
            
            return nil;
        }];
    }];
	}

在方法中，当需要发送请求时就通过封装好的网络类方法发送了一个请求，并且在success回调block中，把网络数据赋值到了self.mailArray中。

前面说过getMailInformations：这个方法不会直接在VC中调用获得数据的，那怎么触发这个网络请求呢？好，现在看VC

	@property(nonatomic,strong)TeacherMailViewModel *viewModel;	
就像前面说好的，需要一个viewModel属性。还需要初始化：

	-(TeacherMailViewModel *)viewModel{
    if (!_viewModel) {
        _viewModel=[[TeacherMailViewModel alloc] init];
        
        [_viewModel getMailInformations];
    }
    return _viewModel;
	}

诶诶诶？怎么getMailInformations：方法直接在viewModel的初始化方法里面调用了？其实他只是相当一个“注册”，真正想要执行command的内部信号的时候需要主动调用下:

	[command execute:nil]

好的，所以一切都做好后，那么需要执行command了，

	- (void)subscribeNextViewModel {
    @weakify(self);
    //将命令执行后的数据交给controller
    [self.viewModel.command.executionSignals.switchToLatest subscribeNext:^(id _Nullable x) {
        @strongify(self);
        [self.tableView reloadData];
    }];
    
    //执行command
    [self.viewModel.command execute:nil];
	}


而且这里订阅了self.viewModel.command的信号，当网络请求里：

	[subscriber sendCompleted];

之后，这里将会订阅到信号，并且刷新一下表格数据：

	[self.viewModel.command.executionSignals.switchToLatest subscribeNext:^(id _Nullable x) {
        @strongify(self);
        [self.tableView reloadData];
    }];

好的，实现tableView的数据源和代理方法吧，它会是这样子的：

	- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section{
    return self.viewModel.mailArray.count;
	}
	- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath{
    MailTableViewCell *cell=[tableView dequeueReusableCellWithIdentifier:teacher_mail_cell_reuse];
    if (cell==nil) {
        cell=[[MailTableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:teacher_mail_cell_reuse];
    }
    
    cell.viewModel_teacher=self.viewModel.mailArray[indexPath.row];
    
    return cell;
	}	

这里的自定义cell也有一个viewModel属性，实际上，他已经不算作一个viewModel了，只是一个单纯的Model，

	#import <UIKit/UIKit.h>
	#import "SNMailModel.h"
	#import "TeacherMailModel.h"
	NS_ASSUME_NONNULL_BEGIN

	@interface MailTableViewCell : UITableViewCell
	@property(nonatomic,strong)TeacherMailModel *viewModel_teacher;
	@end
	
没错吧。然后在自定义cell的实现文件中，绑定数据：

	//老师端数据绑定
    [RACObserve(self, viewModel_teacher.content) subscribeNext:^(NSString *  _Nullable x) {
        self.detail_label.text=x;
    }];
    [RACObserve(self, viewModel_teacher.updateDate) subscribeNext:^(NSString *  _Nullable x) {
        self.date_label.text=x;
    }];

最后，在SNTeacherMailViewController中就可多次的调用subscribeNextViewModel：方法了，不管是viewDidLoad或者viewWillAppear，或者你用了上拉刷新，下拉你都可以调用subscribeNextViewModel：方法，你只需要告诉他你需要数据了，然后他就会请求网络的到数据，然后他知道怎么填充数据，实际上有了RAC，数据改变都是通过类似管道一样的东西散发出去的，只要viewModel中提供数据的mailArray数组发生了改变，那么其他所有订阅了该信号的地方，也都会更改数据，函数响应式编程是个好东西。

你也许产生怀疑了🤨，对于这个场景，我在VC中写好一个网络请求方法，需要的时候调用不就ok了吗，为什么搞这么多东西。是的，MVVM在逻辑很少的情形下，的确感觉有点更麻烦了呢，不过你相信我，当你的VC有超过5个或者10个网络请求后，VC的代码也就开始爆增，而状态又很多的情况下，那么bug会更多，也会更加难维护，后面的维护人员，拿到你的代码2000行，3000，他不会钦佩你多么牛，他只会埋冤你写的代码真正的massive，因为他很难看懂你的代码，同时也很难改，生怕改错什么地方，这是开发了一段时间的人都会体验到的。

ok，你已经又大致的感觉了，但是其中的数据流向你可能还没有搞懂，理解不了数据流向，你可能只会照葫芦画瓢，但是不理解应该怎样用到自己的业务场景中。同时希望下面的图帮到你。

![](/uploads/images/MVVM_IMAGE.png)


