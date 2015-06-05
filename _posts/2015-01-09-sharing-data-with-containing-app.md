---
layout: post
title: "App Extension 和 Containing App共享数据"
description: ""
category: 
tags: [iOS,App Extension]
---
{% include JB/setup %}

#### 前置说明：
即使Extension的bundle 是放在Containing App的Bundle里一起发布的，Extension 和 Containing App也是两个独立的进程，不能互相访问对方的Container。

    解释下Container：在iOS中每一个应用都是一个孤岛。iOS app对文件系统的操作被局限在App沙盘的文件夹中。在一个新的App的安装过程中，安装器会为每个App创建多个Container。每个Container都有各自的作用，Bundle Container持有App的Bundle，Data Container持有用户和App需要使用的Data。App还可以在运行时请求使用额外的Container，比如想iCloud Container.

#### 简单的写下大致步骤

1. 使用Xcode在Project中添加一个Target。
	Xcode会自动给我们创建好一些有用的Controller和配置文件什么。

2. 创建Widget的App ID 和 Provisioning Profile

	因为是一个独立的App，所以也应该要有App ID 和 Profile。
	在Member Center里加上Widget的App ID。注意这个ID一定要是Containing App的下一级。比如Containing App是的ID时com.google.map，那么Widget的ID就一定要是com.google.map.XXX。

	然后惯例，创建相应的Widget App ID的Provisioning Profile。

3. 创建App Group

	因为是两个独立的App，所以不能简单的互相访问对方的资源。这样就要通过一块双方都能访问的文件夹来相互交换资源。Apple的处理方法就是Group，处于同一个App Group下的两个App会有个可以共同访问的文件夹，用于交换数据。

	在Member center中创建一个App Group的ID。
	在Xcode中，将Containing App 和 Today Widget的App Groups功能都打开，并且都选择同一个App Group ID。

	补充说明：
	想要使用App Group container来交换数据有两个常用的方法。
	a. 使用NSUserDefault
		[[NSUserDefaults alloc] initWithSuiteName:APP_GROUP_IDENTIFIER];

	b. 直接找到Group Container的目录。
		可以把数据库文件什么的放里面，就像操作普通的文件目录一样，这个就看应用需求了。
		[[NSFileManager defaultManager] containerURLForSecurityApplicationGroupIdentifier:APP_GROUP_IDENTIFIER];

4. 之后就可以根据需求制作Widget了。
	如果不需要和Container App，上面第三步也可以不要。当然大多数应用还是用得到的。
