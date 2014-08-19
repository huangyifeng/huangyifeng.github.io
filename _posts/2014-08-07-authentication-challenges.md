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

<br/>

#### Delegate中的这个回调方法长啥样

- **NSURLSessionDelegate**

\- (void)URLSession:(NSURLSession *)session didReceiveChallenge:(NSURLAuthenticationChallenge *)challenge completionHandler:(void (^)(NSURLSessionAuthChallengeDisposition disposition, NSURLCredential *credential))completionHandler

- **NSURLConnectionDelegate**

\- (void)connection:(NSURLConnection *)connection didReceiveAuthenticationChallenge:(NSURLAuthenticationChallenge *)challenge

- **NSURLDownloadDelegate**

\- (void)download:(NSURLDownload *)download didReceiveAuthenticationChallenge:(NSURLAuthenticationChallenge *)challenge

看到了没，**NSURLConnectionDelegate** 和 **NSURLDownloadDelegate** 的delegate方法基本上是一样的，都是带了2个参数，所以之后的处理中这两个delegate的处理方法也一致。**NSURLSessionDelegate**有点不一样，带了3个参数，最后多了个completionHandler。

<br/>

#### NSURLRequest需要认证时，系统会怎么处理呢

那得看这个NSURLRequest是用什么发送的。是用**NSURLConnection** 或是 **NSURLDownload** 呢，还是用 **NSURLSession**呢。

	**NSURLConnectionDelegate** 和 **NSURLDownloadDelegate** 这对好基友，有**connection:canAuthenticateAgainstProtectionSpace:** 和 **download:0AgainstProtectionSpace:**方法，而**NSURLSessionDelegate**就没有。

- 如果是用NSURLSession发送的，不论什么authentication是什么类型，都会直接交给delegate来处理
- 如果是用NSURLConnection 或 NSURLDownload发送的，那么会先调用delegate的 canAuthenticate 的方法。这样就可以事先分析下服务器端的属性，包括协议和authentication method。如果你处理这个challenge,那就返回NO，那么系统就会尝试去用户的Keychain看看能不能有什么认证信息。
- 如果Connection和Download这对好基友的delegate，没有实现canAuthenticate方法，并且protection space使用了client certificate或server trust的认证方式，那么系统就当你canAuthenticate方法返回了NO，如果是其他认证方式，系统就当你返回了YES。

接下来呢，如果你已经同意处理Challenge了，而且你发送的Request和**NSURLCredentialStorage**当中都没有有效的Credential的话，那么delegate就会收到**didReceiveChallenge**，当然啦，根据delegate的种类不同，方法名字也不一样。

<br/>

####之后我如何去回应这个Challenge

收到Challenge之后，delegate也有三种处理方式

- 提供一个credential
- 没有credential但是尝试继续访问
- 取消challenge

打个不恰当的比方：你要进一家机关大院，看门的老大爷突然拦住了你，“你干什么的，你找谁啊，把你证件拿出来看看”，这就是一个challenge，你也有三个选择

- 潇洒地把通行证掏出来。（“大爷你看，我是有证程序员”）
- 我没证，但我就是要进去。 （霸道的无证程序员）
- 算了，我不进了。转身就走。(老实的无证程序员)

<br/>
**接下来分别介绍如何处理这3种情况**

####提供一个Credential




<br/>

####没有credential但是尝试继续访问

<br/>

####取消challenge














