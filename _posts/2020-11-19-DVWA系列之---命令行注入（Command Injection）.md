---
layout:     post   				    # 使用的布局（不需要改）
title:      DVWA系列 				# 标题 
subtitle:   命令行注入      #副标题
date:       2020-11-19 				# 时间
author:     L 						# 作者
header-img: img/dvwa.jpg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - DVWA
---
# DVWA系列之---命令行注入（Command Injection）

一、 什么是命令行注入？

命令注入：利用可以调用系统命令的web应用，通过构造特殊命令字符串的方式，把恶意代码输入一个编辑域(例如缺乏有效验证的输入框)来改变网页动态生成的内容，最终实现本应在服务端才能工作的系统命令。一个恶意黑客可以利用这种攻击方法来非法获取数据或者网络资源。

不多说直接上DVWA靶场

1、先是low难度的，源码如下图

![low](https://tva3.sinaimg.cn/large/006O5vizly1gkupq2235uj312k0fjgmc.jpg)

![3.1](https://tva4.sinaimg.cn/large/006O5vizly1gkupq2vm6qj31hc0smgpn.jpg)



可以看到提交IP之后页面是执行一个ping的功能，而且该难度下也是对输入没有任何的检测以及过滤的

我们可以进行任意的命令执行，相当于变向的一个shell吧，利用 “|”（管道符）进行分割执行 net user命令

**命令连接符**


command1 && command2  先执行command1后执行command2
command1 | command2   只执行command2
command1 & command2   先执行command2后执行command1


以上三种连接符在windows和linux环境下都支持


如果程序没有进行过滤，那么我们就可以通过连接符执行多条系统命令。



![s](https://tva4.sinaimg.cn/large/006O5vizly1gkupx5zlh1j30pr0dmaaz.jpg)



2、接下来看medium的源码

![medium](https://tva2.sinaimg.cn/large/006O5vizly1gkupq27ln2j311f0jsmy6.jpg)



从源码上看到较于low，medium加入了一个$substitutions数组，对  ’&&‘，‘；‘ 进行转义为空格，除此之外并没有过多的限制，所以我们还是可以利用 “|” “&”进行命令执行



3、现在看high的源码，发现其对  &、；、|、_、$、||、以及“（” ，“）”等都作了过滤那是不是就没办法了呢？仔细看，对     |     的过滤后面带着一个空格，也就是说过滤的是   |加上空格，而不是   | 所以我们还是可以利用管道符进行命令执行，（一个|打通关。。。）

![high](https://tva3.sinaimg.cn/large/006O5vizly1gkupq2ee0wj31580mit9w.jpg)

![ip](https://tva2.sinaimg.cn/large/006O5vizly1gkuqjer8dvj31hc0smadn.jpg)

利用ipconfig进行查询结果如上



下面是impossible的源码

![im](https://tva4.sinaimg.cn/large/006O5vizly1gkupq2mlbhj31hc0sm410.jpg)

先是检查post提交的 token，然后对输入进行用“  . ” 进行分割然后进行白名单检查，如果不是数字则返回错误，感觉impossible就是没有方法的22333333
