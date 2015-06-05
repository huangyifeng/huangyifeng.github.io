---
layout: post
title: "关于Today App Extension笔记"
description: ""
category: 
tags: [iOS,App Extension]
---
{% include JB/setup %}

原文[http://goo.gl/yKQtim](http://goo.gl/yKQtim)

在Today画面上显示出来的App Extension 也叫Widget。用来给用户看看当前最重要的事情。
千万别再Today Widget里做很复杂的长任务。另外Today widget在锁屏的状态下也可以看到，并且不支持键盘输入。

#### 理解Today Widget
Today Widget应该做到3点

- 确保内容是最新的内容
- 对用户的操作反应恰当
- 性能良好

*尽量避免在Today widget中使用ScrollView，用户想要滚动Today Widget时候会不经意间滚动到Today View。

#### 使用 Xcode Today 模板
使用Xcode Today的模板创建一个Widget，自动会生成一个TodayViewController，一个Info.plist,一个界面文件（storyboard或xib文件）
我们自己的工作就是设计UI，实现View Controller中需要的功能。

#### Design the UI
*建议使用AutoLayout来设计Today Widget的View

#### 更新内容
Today Widget提供了一些API来管理Widget的状态。 参考NCWidgetController和NCWidgetProviding的文档。

为了给Today Widget的更新留出时间，系统有时会给Widget View搞个截图。当Widget可见时，先用截图顶上，等更新完了用实时的Widget的View替换掉截图。

为了实现这个功能，必须先实现NCWidgetProviding这个协议。当Widget收到widgetPerformUpdateWithCompletionHandler:的回调，用最新的数据来更新Widget的View，然后在调用completion handler，同时要带一个参数，来告诉Widget是否要更新画面

- NCUpdateResultNewData（重画 widget的view）
- NCUpdateResultNoData （不用更新Widget）
- NCUpdateResultFailed （更新过程中出错了，可能要提示下用户吧）

#### 指定Widget什么时候应该显示
如果Today Widget只在某些情况下才显示，比如有个新消息什么的，那么可以是用NCWidgetController的setHasContent:forWidgetWithBundleIdentifier:方法。这个方法可以显示或隐藏Today Widget。

上面这个方法可以在Containing App中调用，也可以在Widget中调用。在Containing App中调用这个方法时，Widget可能都没有运行。比如，可以使用APNs的通知，来触发Containing App调用这个方法。

#### 打开Containing App
有些情况下，需要通过Widget打开Containing App。Widget并不是直接告诉Containing App打开，而是使用NSExtensionContext中的openURL:completionHandler:来打开Containing App。当Widget使用这个方法来打开一个URL的时候，系统会先验证这个URL。

#### 测试Today Widget
在iOS上，虚拟机和实机都可以测试Widget。
