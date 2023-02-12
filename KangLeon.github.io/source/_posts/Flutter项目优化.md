---
title: Flutter项目优化--渲染优化
categories:   
 - Flutter
 - 性能优化
tags: 
- flutter
- 性能
---

### 1. 减少build()的耗时：

*  避免在build()方法中执行重复且耗时的工作，因为当父widget重建时，子widget的build()方法会被重复调用.
 
*  避免在build()方法中返回一个超大widget，将widget拆分成多个小widget，各自控制UI的渲染，避免重复渲染一个大的widget。

### 2.使用mobx来进行数据监听，而不是频繁多次进行setState。

### 3.避免离屏渲染：（与iOS概念一样）

避免使用一些会触发saveLayer()的widget：

* 尽量不使用Opacitywidget
* 使用Clipping时避免使用Clip.antiAliasWithSaveLayer
* ShaderMask
* ColorFilter
* 使用Chip且disabledColorAlpha != oxff 时
* 使用Text且存在overfloShader时
避免调用 saveLayer() 的方式：
* 要在图像中实现淡入淡出，请考虑使用 FadeInImage widget，该 widget 使用 GPU 的片段着色器应用渐变不透明度。
* 要创建带圆角的矩形，而不是应用剪切矩形，考虑使用很多 widget 都提供的 borderRadius属性。

### 4.布局方面padding效率大于margin：

### 5.RepaintBoundary：

如果你的自定义View比较复杂，应该尽可能的避免重绘。使用RepaintBoundary者RenderObject.isRepaintBoundary为true可能会有对你有所帮助。

### 6.

* 尽量将setState放在叶子节点，好处是build时影响范围极小，简称局部刷新
![](/uploads/images/flutter/flutter_set_state.png)
* 能不用 Opacity Widget，就尽量不要用，因为这货会粗发GPU一个saveLayer的指令，做Skia的大神说，这个指令相当耗时。
* 使用ListView.builder()而不是直接使用ListView()来构建列表。
* 对于频繁更新的控件（比如倒计时，秒表），使用RepaintBoundary隔离它，让他在一个独立的paint区域。
* 使用const来修饰永远不需要变更的控件。
* 优先使用StateLessWidget，而不是全部用StateFulWidget
* 使用Visibility控件替换if/else，有些小伙伴喜欢else时return一个 占位控件，须不知，这种效率是没有Visibility高效的。













































