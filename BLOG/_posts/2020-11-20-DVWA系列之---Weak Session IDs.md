# DVWA系列之---Weak Session IDs

#### 概述：当用户登录后，在服务器就会创建一个会话(session)，叫做**会话控制**，在seesion存活期间访问页面的时候就不用登录，只需要携带Sesion去访问。

*sessionID作为特定用户访问站点所需要的唯一内容。如果能够计算或轻易猜到该sessionID，则攻击者将可以轻易获取访问权*

*限，无需录直接进入特定用户界面，进而进行其他操作。*

*用户访问服务器的时候，在服务器端会创建一个新的会话(Session)，会话中会保存用户的状态和相关信息，用于标识用户。*

*服务器端维护所有在线用户的Session，此时的认证，只需要知道是哪个用户在浏览当前的页面即可。为了告诉服务器应该使*

*用哪一个Session，浏览器需要把当前用户持有的SessionID告知服务器。用户拿到session id就会加密后保存到 cookies 上，*

*之后只要cookies随着http请求发送服务器，服务器就知道你是谁了。SessionID一旦在生命周期内被窃取，就等同于账户失窃。*

**Session利用的实质** ：

*由于SessionID是用户登录之后才持有的**唯一认证凭证**，因此黑客不需要再攻击登陆过程(比如密码)，就可以轻易获取访问权*

*限，无需登录密码直接进入特定用户界面， 进而查找其他漏洞如XSS、文件上传等等。*

**Session劫持** : *就是一种通过窃取用户SessionID，使用该SessionID登录进目标账户的攻击方法，此时攻击者实际上是使用*

*了目标账户的有效Session。如果SessionID是保存在Cookie中的，则这种攻击可以称为**Cookie劫持**。SessionID还可以保存*

*在URL中，作为一个请求的一个参数，但是这种方式的安全性难以经受考验。*🕵️‍♂️

一、low级别

![6.1](https://tvax4.sinaimg.cn/large/006O5vizly1gky78wap2xj31hc0smq7s.jpg)

low级别还是，没有任何的针对性防护，抓包可以发现，dvwaSesion的cookie，每重放一次，dvwaSesion增加一：这里要利用一下Firefox里面的hacker插件，F12打开查看网络，可以看到此时的cookie，在hacker里面选择cookie登录，可以看到，利用cookie直接绕过密码成功登录

![6.3](https://tvax2.sinaimg.cn/large/006O5vizly1gky7l8gl6dj31hc0smgpd.jpg)

![6.5](https://tvax1.sinaimg.cn/large/006O5vizly1gky7mu0qd7j31hc0smq77.jpg)

源码附上：

![l](https://tva2.sinaimg.cn/large/006O5vizly1gky7os1vitj30ot0b2mxk.jpg)

从源码也可以看出来，每次的seesion ID都是上一次的ID+1，除此之外没有什么特别的

二、medium级别

来看一下源码：

![m](https://tva3.sinaimg.cn/large/006O5vizly1gky7t66tigj30tw07pq33.jpg)

一目了然，这次的cookie不再是+1了，而是改为了时间戳，但也不是一个难点吧