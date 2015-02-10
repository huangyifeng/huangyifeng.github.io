---
layout: post
title: "Hello Jekyll"
description: ""
category: 
tags: []
---
{% include JB/setup %}

用Jekyll-Bootstrap确实挺方便的，这篇做为一个尝试吧。

顺便记录几个MarkDown常用的标记。

#标题 1级

##标题 2级

###标题 3级

####标题 4级

#####标题 5级

######标题 6级

#######标题 7级 （对不起，没7级，最多6级）

    这里开始为引用的代码

> 这里是区块引用
>
>>这里是二级区块引用
>
>这里是区块引用


- list item1
- list item2
- list item3

1. list item1
8. list item8
3. list item3
7. list item7
5. list item5(完全不管前面的序号是几)

===============================

这里是引用的[链接](http://huangyifeng.github.com)

这里是参考引用的[链接][abc]，多个地方使用同一个链接时，还是挺有用的

[abc]: http://www.google.com "鼠标放在这里会有字"


很多普通字体里突然出现的**黑体\<strong\>**和*斜体\<em\>* 字体

很多普通字体里突然出现的`红色`字体。

怎么插入图片？那个同学，你来回答这个问题。别看别人，就是你。

![3年Z组银八先生](\assets\test.png)

#####常用命令

- 创建Post

	rake post title="Hello World"

- 运行预览服务器

	jekyll serve

	A development server will run at http://localhost:4000/

	官网上的jekyll --serve 已经不能用了。








先记录这些，之后遇到有用的再陆续补充。
