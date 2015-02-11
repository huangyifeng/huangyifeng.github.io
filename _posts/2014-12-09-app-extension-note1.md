---
layout: post
title: "App Extension 笔记 1"
description: ""
category: 
tags: [iOS,App Extension]
---
{% include JB/setup %}

##概述：
从iOS8.0和OS X v10.10开始，Apple提供了Extension的功能。在原本的应用外，也可以使用应用的一些功能。

###Extension的几个类型
iOS和OS X中，定义了几种类型的Extension，每一种都与系统中某个区域联系在一起，支持Extension的某个系统区域就称为Extension Point。每种类型的Extension提供用法和API都不一样。

Extension类型

- Today (iOS and OS X)， 也称为Widget
- Share (iOS and OS X)
- Action (iOS and OS X; UI and non-UI variants)
- Photo Editing (iOS)
- Finder Sync (OS X)
- Document Provider (iOS; UI and non-UI variants)
- Custom Keyboard (iOS)

###创建与发布
在某个工程下创建新的Target，Xcode为每种target提供了模板。每个App可以使用多个Extension，包含了一个或多个Extension的App称为Containing App。

提交Containing App到App store，用户安装了Containing App之后，自动安装Extension。安装之后用户必须手动的开启Extension，才能使用Extension。

###Extension的设计原则
快速，线性，单任务

UI设计方面：在Extension Point与个性设计风格之间找个平衡点。尽量融入系统风格。

##App Extension 是如何工作的

###生命周期
App Extension不是一个App，所以生命周期与App不一样。想要启动Extension的那个app称为host app。Host app定义了Extension的context。在收到用户动作事件之后，给Extension发送request来启动Extension的生命周期。一般在处理完成收到的Request之后，Extension就被终止了。
 
<img src="https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/Art/app_extensions_lifecycle_2x.png" width="700px" />

第一步，用户操作，host app给Extension发送Request

第二步，系统根据host app发送的Request初始化Extension，并建立两者之间的通信通道。Extension将自己的View显示在host app的context中。

第三步，用户执行或取消Extension中的任务，并且Dismiss掉。

第四步，系统终结掉Extension。

###Extension怎么通信
<img src="https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/Art/detailed_communication_2x.png" width="700px" />

Extension主要与host app通信，很传统的Request和Response方式。

Extension与Containing app不能直接通信。通常来说，Extension运行的时候，Containing app都不一定处在运行状态。

- Extension 和 Containing app可以共同读写一块私有共享容器中的共享数据。
- 只有Today widget可以调用 NSExtensionContext 的 openURL:completionHandler:方法来打开Containing app。

Host app与Containing app互相不能通信。

###Extension不能用的API

- sharedApplication
- NS_EXTENSION_UNAVAILABLE里标记的所有的头文件
- 访问摄像头和麦克风
- 运行长后台任务
- 用AirDrop接收数据

