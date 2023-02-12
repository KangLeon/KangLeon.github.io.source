---
title: Fastlane 打造自动化打包发布环境
categories:   
 - iOS
tags: 
- iOS
- Fastlane
- 自动化
---

### 提要：

本篇博客涉及内容：

* 借助fastlane打造自动化打包、签名、导出ipa流程 
* 打包成功后上传到蒲公英
* 并且通知测试人员测试

### 为什么使用fastlane：

正常产品开发完成之后，我们都需要给测试人员打包，又是测试包，又是生产包的，打一次包需要浪费十几分钟的时间，
  甚至有时候，你刚打完包，产品过来告诉你某个地方需要微调一下，但是没办法，只好改完bug，继续打包，
  就这样可能一上午或者一下午就这样浪费了，所以有一个能够自动化打包的工具不仅能够为我们节省大量的时间。

  Fastlane是一款为 iOS 和 Android 开发者提供的自动化构建工具，它可以帮助开发者将 App 打包、
  签名、测试、发布、信息整理、提交 App Store 等工作完整的连接起来，
  实现完全自动化的工作流，如果使用得当，可以显著的提高开发者的开发效率。

  其实自动化打包的工具有很多，比较流行的有Jenkins和fastlane，原来尝试过Jenkins,感觉这个工具比较麻烦，
  需要配置的东西非常多，还需要仓库地址等等很多信息，不像fastlane感觉是傻瓜式的，非常简单。
  
### 开始：

#### 1.在安装fastlane前必须确认是否已经安装了ruby：

	ruby -v
	
你可以用brew安装也可以用rvm安装，不过推荐使用rvm安装，因为遇到一个比较搞的点是，fastlane依赖的bundler比较旧，如果你的ruby版本比较新的话，fastlane是没法适用的。rvm可以提供ruby版本切换功能。

#### 2.确认是否安装了Xcode命令行工具：

	xcode-select  --install

如果出现

	xcode-select: error: command line tools are already installed, use "Software Update" to install updates

证明已经安装成功，如果出现安装提示，按照提示顺序安装即可。

#### 3.安装fastlane：

	sudo gem install fastlane

在这之间会出现这个提问：回答y即可

有时会遇到报错You don't have write permissions for the /usr/bin directory.
即使我们在安装命令前加上sudo，也不行。这时我们可以使用：

	sudo gem install fastlane -n /usr/local/bin 
 
这句就可以了。

你可以验证下安装结果：

	fastlane -v

如果出现版本号并且没有报错，证明安装成功：

	fastlane installation at path:
	/opt/homebrew/Cellar/fastlane/2.180.1/libexec/gems/fastlane-2.180.1/bin/fastlane
	-----------------------------
	[✔] 🚀
	fastlane 2.180.1

#### 4.cd到工程目录：

初始化的过程中会出现下面的选项：

![](/uploads/images/fastlane/IMG_1.jpeg)

* 第一个选项的意思是：自动截屏。这个功能能帮我们自动截取APP中的截图，并添加手机边框（如果需要的话） 
* 第二个选项的意思是：自动发布beta版本用于TestFlight
* 第三个选项的意思是：自动发布到AppStore 
* 第四个选项的意思是：手动设置。

输入对应的数字即可，我在这里选的是第四个（大家可根据自己需要选择）

当输入4后，出现以下内容时，一直惦记enter键就可以：

![](/uploads/images/fastlane/IMG_2.jpeg)

安装成功后会提示：

![](/uploads/images/fastlane/IMG_3.jpeg)

现在可以到你的工程目录中查看下，是否已经生成一个fastlane文件夹：

![](/uploads/images/fastlane/IMG_4.png)

现在打开fastlane文件夹，打开Appfile文件，当然你可以在终端打开编辑：

	 app_identifier("***.***.****") # The bundle identifier of your app
	 apple_id("***@*******") # Your Apple email address
	 team_id "**********" # Your team id

按照注释内容进行内容填写即可，填写完保存。

然后打开Fastfile文件：

	#定义打包平台
	default_platform(:ios)
	#可以设置导出ipa包的时间，作为ipa名字的标识
	currentTime = Time.new.strftime("%Y%m%d%s")
	#可以设置导出ipa包的打包方式，也可以作为ipa名字的标识
	configuration="AdHoc"
	
	#指定项目的scheme的名称
	scheme = "Runner"
	
	#指定项目的打包方式
	exportMethod = "ad-hoc"
	
	#蒲公英api_key和user_key
	api_key="57b0dd6d0fadd306c0ed38287db8e997"
	user_key="a947a0bdea922913b939d49c506ae86d"
	
	platform :ios do
	  desc "OYE IOS FastLane自动上传"
	  lane :oye_pgy do
	   gym(
	    # add actions here: https://docs.fastlane.tools/actions
	    scheme: "#{scheme}",#指定项目的scheme的名称
	    output_directory: "/Users/******/Desktop", # 打包后的 ipa 文件存放的目录
	    configuration:"#{configuration}",
	    output_name: "#{scheme}_#{configuration}_#{currentTime}",  # ipa文件名
	    # 指定打包所使用的输出方式：app-store、ad-hoc、enterprise、development
	    export_method: "#{exportMethod}",
	    # 自动管理证书的时候，Xcode 9及以上没有权限获取钥匙串里面的证书，必须加上这个才能打包成功
	    export_xcargs: "-allowProvisioningUpdates"
	    )
	   puts "开始上传蒲公英"#
	   pgyer(api_key: "#{api_key}", user_key: "#{user_key}")
	  end
	end

提示：

1.蒲公英的api_key和user_key可以到：“我的-基本资料-api信息”中找到

2.其他字段的作用自己探索吧，我也没有探索完所有字段的作用

#### 5.设置上传到蒲公英

安装蒲公英的 Fastlane 插件：

	fastlane add_plugin pgyer

在此期间会有如下提问，选择y即可

	Should fastlane modify the Gemfile at path  
	      '/Users/iOS002/Desktop/MyNewTest/Gemfile' for you? (y/n)   y

在此期间还会需要输入你电脑的解锁密码。
稍等几秒钟，如果出现：

	Successfullt installed plugins即代表成功了

插件安装好后，会多出 Gemfile、Gemfile.lock 、Plugfile（你安装了什么插件，就会在该文件中显示）这三个文件，不用管。提前说下，如果打包上传完成，会在当前文件夹下生成工程对应的 dSYM文件和 iPa 文件。

#### 6.开始打包：

fastlane oye_pgy #这个是在你fastfile中的lane： *** do 中的那个自己定义的名词。

上传的过程中难免会有问题，请按照自己的报错日志进行解决。

如果一切正常并且上传到了蒲公英，lucky dog！

#### 7.设置发包后通知给测试人员：

这个是蒲公英提供的设置，你可以你的应用下的“成员管理-添加新成员”中进行操作，当发包成功后就会给下面的成员发送邮件或短信，good luck，你又节省了很多时间










































	
	