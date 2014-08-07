---
layout: post
title: "Authentication Challenges"
description: ""
category: 
tags: [iOS, Security]
---
{% include JB/setup %}

Apple文档笔记 
[Authentication Challenges](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/URLLoadingSystem/Articles/AuthenticationChallenges.html#//apple_ref/doc/uid/TP40009507-SW1)

发送了NSURLRequest之后，经常会碰到Authentication Challenges (之后缩写成AC)，或者某个连接的服务器需要一个Credential。碰到这个情况**NSURLSession**, **NSURLConnection** 和 **NSURLDownload**都会调用各自的**delegate**，我们可以根据情况对这些Challenge分别处理。

	只有在response头部存在WWW-Authenticate的时候，上述者3个类才会调用各自的delegate。其他类型的认证不需要这个头部信息，比如：proxy认证和TLS trust验证。


#### Delegate中的这个回调方法长啥样

- **NSURLSessionDelegate**

\- (void)URLSession:(NSURLSession *)session didReceiveChallenge:(NSURLAuthenticationChallenge *)challenge completionHandler:(void (^)(NSURLSessionAuthChallengeDisposition disposition, NSURLCredential *credential))completionHandler

- **NSURLConnectionDelegate**

\- (void)connection:(NSURLConnection *)connection didReceiveAuthenticationChallenge:(NSURLAuthenticationChallenge *)challenge

- **NSURLDownloadDelegate**

\- (void)download:(NSURLDownload *)download didReceiveAuthenticationChallenge:(NSURLAuthenticationChallenge *)challenge

看到了没，**NSURLConnectionDelegate** 和 **NSURLDownloadDelegate** 的delegate方法基本上是一样的，都是带了2个参数，所以之后的处理中这两个delegate的处理方法也一致。**NSURLSessionDelegate**有点不一样，带了3个参数，最后多了个completionHandler。


#### NSURLRequest需要认证时，系统会怎么处理呢

那得看这个NSURLRequest是用什么发送的。是用**NSURLConnection** 或是 **NSURLDownload** 呢，还是用 **NSURLSession**呢。

**NSURLConnectionDelegate** 和 **NSURLDownloadDelegate** 这对好基友，有**connection:canAuthenticateAgainstProtectionSpace:** 和 **download:canAuthenticateAgainstProtectionSpace:**方法，而**NSURLSessionDelegate**就没有。

所以呢

- 如果是用NSURLSession发送的，不论什么authentication是什么类型，都会直接交给delegate来处理











<!-- #### 首先，你得想清楚，我打算如何去回应这个Challenge -->











