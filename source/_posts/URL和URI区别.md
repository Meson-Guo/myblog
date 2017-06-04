---
title: URL和URI区别
date: 2016-11-18 15:23:45
categories: 学习笔记
tags:
- URL URI
description: 用自己的方式讲述URL和URI的区别
---
<!-- more -->

今天研究了一下URL和URI的区别，对于我们大多数人来说，我们更熟悉URL(Uniform Resource Locator),没错，URL就是我们访问Web资源时，浏览器上输入的网页地址。
URI(Uniform Resource Identifier)统一资源标识符。URI就是由某个协议方案表示的资源定位符 采用HTTP协议就是http,初次之外还有mailto.telnet,file等等
从我的角度理解的角度先对他们做一个总体的比较吧。URI是比URL更高层次的抽象，换句话说URL属于URI的一种。URI是一种资源定位机制，比较大概的定位了资源，并不局限于我们所说的客户端和服务器，但是URL就是定位了网上的一切资源，只要是网上的资源都有唯一而URL。从《HTTP权威指南》中可以知道，URI表示请求服务器的路径，只是定义了一个资源，而URL同时还要说明如何访问这个资源。
	
# 详细说明

## URI通常包括三个部分：
1，方案或者说访问资源的机制：HTTP://等等
2.存放资源的主机名
3.资源本身，一般由路径表示。
URI可以是绝对的，也可以是相对的。它就是纯粹的语法结构，对URI字符串进行解析，不会考虑她所指定的方案，不对主机执行查找的操作。不会构造依赖于方案的流处理程序。
	
	
作为对比，URL所映射的类必须是绝对的（你可能听过相对的URL，但是那是基于主机的相对，与此不同），URL始终指定一个方案，字符串会按照其方案进行解析，会对资源建立流处理程序。
也就是说它支持解析的语法运算以及查找主机和打开到指定资源的链接之类的网络I/O操作。
给大家列出几个URI的例子：
我们可以举个简单的例子：
	
http://www.ustcstu.com/1231/software/student.html
这个URI表示可以通过HTTP协议访问资源，位于主机www.ustcstu.com上通过路径/1231/software/访问。
	
&lt;a href=mailto：mesonguo@163.com&gt;这是我的邮箱&lt;/a&gt;
这个URI就指向了一个邮箱
	
当然有时候可能会出现相对的URI
&lt;a href="software/student.html"&gt;这是我的邮箱&lt;/a&gt;
在Java的类库中，URI中不含有任何访问资源的方法，它的唯一作用就是解析，但是但我们URL时，会发现有可以访问资源的流和方法。
## URL通常分为三部分：
1，协议
2，主机IP地址
3，主机资源的具体地址。
URL就是internet的地址薄。
举个例子吧
http://www.ustcstu.com/software/student.html
这是一个HTTP的URL，使用超文本传输协议，提供文本信息的服务资源。其中 www.ustcstu.com是计算机域名在software下的student.html。当然这里使用的是http，有时候也会用ftp（文件传输协议），主要用来传输软件和大文件的：telenet（远程登录），主要用于远程交谈，以及文件调用。