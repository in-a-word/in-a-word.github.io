# 记一道有趣的CTF题---寻找蒋璐源的秘密（极客大挑战2019-securtfile）

BUUCTF上的一道web题，难搞的ctf题千篇一律，有趣的题目万里挑一，就把这个好玩的题做个小记。



打开环境，就一个HTML页面

![1](https://tva3.sinaimg.cn/large/006O5vizly1gl539lcivaj31hc0smac9.jpg)

F12查看

![image](https://tvax2.sinaimg.cn/large/006O5vizly1gl53b79p9dj31uo11iako.jpg)

我们可以发现上图其中隐藏了一个按钮，点击，来到下图



![image](https://tva1.sinaimg.cn/large/006O5vizly1gl53csr0dqj31uo11i49l.jpg)

点击select查看

![image](https://tva1.sinaimg.cn/large/006O5vizly1gl53doqy49j31uo11i7ek.jpg)

跳转到end.php，，，，没了

小赖皮不讲武德

burp抓包分析

![image](https://tva1.sinaimg.cn/large/006O5vizly1gl53fdm650j31uo11iguh.jpg)

发现了隐藏其中的secr3t.php     ········🐱‍🏍

访问查看

![image](https://tva4.sinaimg.cn/large/006O5vizly1gl53h6ebgqj31uo11i7fe.jpg)

到这一步才算是刚刚开始，找出了源码，可以看到flag就在flag.php里，直接访问flag.php

![image](https://tva1.sinaimg.cn/large/006O5vizly1gl53jvzg4qj31uo11iahb.jpg)

当然是不行的，小赖皮怎么那么容易就让你拿到flag，稍微代码审计一下php源码

可以发现if判断语句里面对一些php的伪协议、../等做了过滤，但是漏了一个    php://filter

构造payload：

```
php://filter/cover.base64-encode/resource=flag.php
```

![image](https://tva1.sinaimg.cn/large/006O5vizly1gl53ryyperj31uo11i47n.jpg)

成功读取出flag

![image](https://tvax1.sinaimg.cn/large/006O5vizly1gl53st9wbdj31uo11ih44.jpg)

本质上这个题就是考察了一些PHP伪协议的应用

![QQ浏览器截图20201128193751](https://tvax2.sinaimg.cn/large/006O5vizly1gl54d6aj0aj30ok0etdgq.jpg)