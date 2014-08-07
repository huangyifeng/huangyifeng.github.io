---
layout: post
title: "关于证书、秘钥等等笔记"
description: ""
category: 
tags: [iOS, Security]
<!-- tagline : iOS -->
---
{% include JB/setup %}

看了Apple公司的文档，记录下些东西，不然看过就忘了。

原文地址[Certificate, Key, and Trust Services Concepts](https://developer.apple.com/library/mac/documentation/Security/Conceptual/CertKeyTrustProgGuide/02concepts/concepts.html)

###证书，秘钥，Identity的概念

证书是一系列数据的集合，包括哪些东西呢

- 证书发行人
- 证书持有人
- 有效期限（期限外都不是有效证书）
- 证书主人的公钥
- 证书扩展（Certificate extensions）包括了些额外的信息，比如能否使用该证书私钥之类的。
- 数字签名，确保证书没被篡改

每个证书都是另外一个证书认证的，形成了一条证书链，一直追溯到`根证书`。证书发信人就称为`CA`（certification authority）。

每个公钥都是 公钥-私钥对的一部分。公钥谁都可以获得，私钥自己拿着，别随便给人。公钥加密，私钥解密，或者私钥加密公钥解密。为了能加密和解密数据，指定用户必须既有公钥也有私钥（我估计这里指的不是同一对公钥私钥）。一个证书和相关的私钥的组合被称为`identity`。`Certificate`, `Key`, 和 `Trust Services` （缩写CKT吧）包括了查找某个identity相关的证书和秘钥的的功能，以及给定查找条件找到某个identity的功能。查找条件包括了对使用秘钥的许可。

在OS X和iOS中，秘钥和证书都是存在`keychain`中的。keychain就是个加过密的数据库，存点私钥啦，和其他一些保密的东西。

###Certificate, Key, and Trust Services and CDSA

在iOS中，`Keychain Services API`提供了所有操作keychain中东东的API

在OS X中有一套开源的东西了，叫CDSA（Common Data Security Architecture 通用数据安全架构）。这东西有个编程接口 CSSM（ Common Security Services Manager）通用安全服务管理器。

###Policies and Trust

数字证书中的某些属性，也就是那个证书扩展，指明了证书的`信任等级` (level of trust)。什么是证书的信任等级呢，简单的讲，就是尝试在回答一个问题，“这个证书要做这个事情，我是否该信任他”。`信任策略`(trust policy)就是一系列的规则，这些规则来指明**如何评估某个证书，来看看它对于某个信任等级是否是有效的** (这段我也没看懂)

举个栗子，在iOS X中AppleX509TP模块实现了一个信任策略，称为**S/MIME策略**，这个策略除了对证书的基本的验证外，还指定了如何核实一个邮件地址。当你用CKT API建立了一个信任评估，你指定使用什么策略来评估信任。又举个栗子，如果你指定SSL策略，你也就是在问，通过网络建立一个安全连接的时候，这个证书能否被信任。














