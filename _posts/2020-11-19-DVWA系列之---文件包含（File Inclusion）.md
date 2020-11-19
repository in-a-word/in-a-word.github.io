# DVWA系列之---文件包含（File Inclusion）



#### 		概述：文件包含是指当服务器开启allow_url_include选项时，就可以通过php的某些特性函数（include()，require()和include_once()，require_once()）利用url去动态包含文件，此时如果没有对文件来源进行严格审查，就会导致任意文件读取或者任意命令执行。文件包含漏洞分为本地文件包含漏洞与远程文件包含漏洞，远程文件包含漏洞是因为开启了php配置中的allow_url_fopen选项（选项开启之后，服务器允许包含一个远程的文件）

一、low级别

源码分析

![l](https://tvax4.sinaimg.cn/large/006O5vizly1gkurjrekb1j30ho07mjrd.jpg)

短短的一句话，一目了然

一点点的防护都没有，现实中怎么才能遇到这种洞呢🤣

由于没有任何的保护措施，我们可以构造url进行文件包含

本地文件包含：

​		绝对路径包含：

![3.1](https://tva4.sinaimg.cn/large/006O5vizly1gkurs6n12aj31hc0smacu.jpg)

上图展示的是用绝对路径包含的同级目录下的1.txt文件

​			相对路径读取php.ini文件：

![3.2](https://tvax3.sinaimg.cn/large/006O5vizly1gkurx0weg8j31hc0smmzr.jpg)

除了本地包含还可以进行远程包含，可以在远程服务器端存储一个一句话木马文件，然后进行远程文件包含一句话木马，然后使用菜刀进行连接，这里就不进行具体演示了

二、medium级别

源码分析

![m](https://tva4.sinaimg.cn/large/006O5vizly1gkurynhzvvj30ko08ymxb.jpg)

由上面的PHP代码可以看出来，medium级别的使用了PHP的str_replace函数进行了对“http：//，https：//”以及“../，..\”的处理

但是该函数是不安全的，果是对一个数组进行查找，但只对一个字符串进行替换，那么替代字符串将对所有查找到的值起作用。

也就是说，该函数可以使用双写的方法进行绕过

所以，在low的基础上加上一个双写的方法就可以了

构造url远程包含百度的一个页面：

![3.3](https://tva2.sinaimg.cn/large/006O5vizly1gkusdo10zxj31hc0smmzm.jpg)



三、high级别

源码分析

![h](https://tva2.sinaimg.cn/large/006O5vizly1gkusfc73o0j30mv0a0wep.jpg)

high级别的可以看到，采用的是白名单的防护模式，只允许file开头的文件名或者include.php，其他的则会报错。include.php已经被写死，没什么搞头，我们来从file*下手

这里就要了解一个东西-------

file伪协议：

用于访问本地文件系统的文件 **条件**：allow_url_fopen: off/on allow_url_include: off/on

file：///后面+本地文件的路径

这里我要file包含了同文件夹下的1.txt文件

![3.4](https://tva1.sinaimg.cn/large/006O5vizly1gkusrzabu0j31hc0smact.jpg)



impossible理论上应该是没有漏洞的，它要白名单进行控制，除了include.php,file1.php,   file2.php  file3.php，其他的参数皆不可

![i](https://tva1.sinaimg.cn/large/006O5vizly1gkusfcembjj30vd09wq38.jpg)

#### 总结：

 include()函数对文件后缀名无要求，而对其中的语法有要求，即使后缀名为txt，jpg也会被当做php文件解析，只要文件内是形式就可以执行，但是如果不是php语法的形式，即使后缀为php，也无法执行。

除了file伪协议，php还有其他的一些伪协议如php://input等

由此可见，文件包含不仅仅可以进行文件泄露，还可以进行恶意代码执行等