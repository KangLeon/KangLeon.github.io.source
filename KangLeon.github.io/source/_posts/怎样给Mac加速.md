---
title: 怎样给Mac加速
categories:   
 - 经验分享
tags: 
- 开发经验
---

# 怎样给Mac加速

## 进程优化

### 一、Windows Server进程优化

![](/uploads/images/怎样给mac加速/windows_server.png)

这是一个负责窗口调度显示的进程，是系统进程，因为更多的动画和阴影处理会增加windows_server进程的处理工作，所以导致电脑在卡顿的时候学上加霜，解决办法就是，让各种效果返璞归真，减少特效。

问题处理

勾选动态效果，勾选降低透明度

![](/uploads/images/怎样给mac加速/关闭显示效果.png)

### 二、mds、mds_stores、mdworker 进程优化

这些进程都是系统进程，是Spotlight功能创建的进程

![](/uploads/images/怎样给mac加速/系统进程.png)

Spotlight 中文名称为 聚焦，就是按下 Command + 空格 弹窗的那个

![](/uploads/images/怎样给mac加速/Soptlight.png)

`mds、mds_stores、mdworker` 是 Spotlight 的组成部分：

- mds
  - 元数据服务器。
- mdworker
  - 元数据服务器 worker 进程，主要负责文件的索引。
- mds_stores
  - 元数据存储服务。

####  问题处理

因为我很少使用这些功能，所以我选择了禁止 聚焦 索引文件。

  `sudo mdutil -a -i off`
  
如果需要再次使用，可以通过以下命令重新开启。

  `sudo mdutil -a -i on`
  
###   三、node进程优化

当我们运行项目的时候，假如长时间运行工程，node进程，非系统进程占用的内存会越来越大，曾经见到这个进程占用内存会到达5G左右
  
![](/uploads/images/怎样给mac加速/node进程.png)

#### 问题处理

那么我们其实可以主动Ctrl+C关掉不在运行的工程，以清理被占用内存

## 物理降温

### 一、增加降温设备