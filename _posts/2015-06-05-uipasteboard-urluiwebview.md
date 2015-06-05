---
layout: post
title: "UIPasteboard url属性在UIWebView中无法粘帖的问题"
description: ""
category: 
tags: []
---
{% include JB/setup %}

今天调查了一个关于UIPasteboard url属性在UIWebView中无法粘帖的问题。搜遍Google也没找到类似问题，所以记录一下，方便后来人。

##### iOS发生版本号：
    iOS7 至 iOS8.1.3发生
    iOS8.3已修复。

##### 现象：
    使用了 [UIPasteboard generalPasteboard].URL = url，设置UIPasteboard的URL属性之后，在UIWebView中的部分输入框中，长按并选择粘帖无效。

##### 调查结论：
    判断为iOS的系统Bug。
    无效的输入框：
        对于HTML中的<textarea>标签做成的输入框，粘帖无效。
        对于HTML中的<input>标签做成的输入框，粘帖无效。
    有效的输入框：
        对于HTML中的<div contenteditable="true">标签做成的输入框，粘帖有效。

##### 解决方法：
    使用 [UIPasteboard generalPasteboard].string = [url absoluteString]; 可以绕开这个Bug。

[测试代码](\assets\WebPaste.zip)
    