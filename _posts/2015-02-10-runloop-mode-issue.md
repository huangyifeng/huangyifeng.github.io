---
layout: post
title: "Runloop Mode问题的一点心得"
description: ""
category: 
tags: [iOS,RunLoop]
---
{% include JB/setup %}

#### 简单介绍下RunLoop

Runloop，顾名思义就是一个不断在重复运行的环。可以用来 安排一些代码的执行 以及 处理某些输入的事件  (sockets, ports, 文件, 键盘, 鼠标, timer 之类的)。

每个NSThread都有一个自己的Runloop，可以通过currentRunloop 来取得。主线程的Runloop在应用启动的时候，就由系统自动创建并运行了。子线程的Runloop需要自己手动运行。

Runloop每次运行的时候，如果发现没有需要处理的事件或代码，就会退出。

#### Runloop 的 Mode 是怎么回事
    
Runloop Mode 本质上就是个字符串，所以除了系统常用的Mode之外，也可以自定义Mode，一般很少用到。

**每种Runloop Mode 都和 某些特定的事件 或 安排执行的代码关联在一起。** 这样能让线程有活干的时候忙起来，没活干的时候去休息，这就是Runloop设计的目的。


上面粗体部分这样讲有点抽象，我再解释一下，就是说，某些 事件 或者 代码的执行 会注册在某种 Mode 下，只有在Runloop使用这种Mode运行的情况下，这些事件和代码才会被处理。举个例子，NSTimer默认会注册在NSDefaultRunLoopMode下，只有在Runloop使用NSDefaultRunLoopMode这个Mode运行的时候，Timer才能被触发。当然，我们也可以指定Timer注册在一些别的什么模式下。


#### iOS中常用的Runloop Mode
NSDefaultRunLoopMode

    RunLoop大部分的情况都是在这个Mode下运行。这个Mode可以处理除了NSConnection对象之外的其他大部分情况。

NSRunLoopCommonModes

    看这个名字Modes，是个复数。说明他不是一个Mode在战斗，不是一个Mode ~~。


#### 关于Runloop的几个常用API
- **NSObject**

	a. performSelector:withObject:afterDelay:

	b. performSelector:withObject:afterDelay:inModes:

	这两个API，是设置一个Timer，n秒之后，当前线程的RunLoop下次运行的时候，调用selector里的方法，a方法中不带inModes参数，默认使用NSDefaultRunLoopMode。

	这个方法只是 Schedule 一个 Selector任务，并不去执行这个Selector，真正执行是要等到用 inModes中的那些参数Mode来执行当前RunLoop时，才会真正运行这个Selector，当然要等Delay的时间到了才可以。

	举个例子

	我曾经使用 a方法来Schedule一个Selector，但是之后被没有执行，通过调试后发现是由于UIScrollView在滚动时，MainRunLoop的Mode是UITrackingRunLoopMode，而Delay参数所设置的Timer默认注册在NSDefaultRunLoopMode下，所以Timer根本就没被触发。

	<br/>
	c. performSelectorOnMainThread:withObject:waitUntilDone:

	d. performSelectorOnMainThread:withObject:waitUntilDone:modes:

	这个方法就是把Selector扔到MainRunLoop的末尾去，不加modes参数的话，默认是NSRunLoopCommonModes，这个Modes包含多种Mode，而且MainRunLoop是由系统不停地在运行的，所以大部分的情况下，这个Selector就直接可以在主线程上运行了，这个方法在需要更新画面显示的时候经常使用。

	有一点要注意，如果调用这个方法的地方本身是主线程，而且waitUntilDone还是YES的话，那么就不会把Selector扔到MainRunLoop的末尾，而是直接忽略掉那个modes参数，管你需要什么modes，反正老子是主线程等不起，就直接给运行了。

- **NSRunLoop**

	a.runMode:beforeDate:

	b.acceptInputForMode:beforeDate:

	看了API文档，感觉这两个方法没什么区别，除了a带了一个BOOL类型的返回值。

	这个两个方法都是使用指定mode去把RunLoop跑一圈，如果没有什么InputSource能处理的，就直接返回。不然就处理掉应该处理的事件 或是 等到那个Date再返回。

	那为什么又2个差不多的API呢，Google一下，答案说是历史遗留问题。

	<br/>
	c.run

	d.runUntilDate:

	这两个方法差不多，c是在一直持续运行下去，d是在一段时间内持续运行.如果d的参数是][NSDate distantFuture]，那就没什么区别了。

	本质上就是用NSDefaultRunLoopMode，不停的调用runMode:beforeDate:这个方法。
