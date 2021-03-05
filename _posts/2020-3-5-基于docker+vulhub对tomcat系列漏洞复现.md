# 基于docker+vulhub对tomcat系列漏洞复现

### 一、Tomcat简介

​		Tomcat 服务器是一个免费的开放源代码的Web 应用服务器，属于轻量级应用 服务器，在中小型系统和并发访问用户不是很多的场合下被普遍使用，是开发和调试JSP 程序的首选。对于一个初学者来说，可以这样认为，当在一台机器上配置好Apache 服务器，可利用它响应 HTML （ 标准通用标记语言下的一个应用）页面的访问请求。实际上Tomcat是Apache 服务器的扩展，但运行时它是独立运行的，所以当运行tomcat 时，它实际上作为一个与Apache 独立的进程单独运行的。

#### 一）CVE-2017-12615

##### 	漏洞原理：

​			该漏洞是tomcat的ApacheTomcat服务组件开启了HTTP的PUT方法导致的任意文件上传漏洞，该漏洞所影响的版本从ApacheTomcat7.0.0-7.0.79以及8.几的零星几个版本

漏洞环境：

docker+vulhub   Apache Tomcat/8.5.19

![1](//tvax4.sinaimg.cn/large/006O5vizly1go7yuicfnuj30wy0h77as.jpg)



访问漏洞开启环境的IP+8080端口

![2](//tva3.sinaimg.cn/large/006O5vizly1go7yyh0a16j31h50re103.jpg)

使用burp抓取数据包进行修改，注意：改后的PUT路径要以/结尾，不然会上传失败

![3](//tvax4.sinaimg.cn/large/006O5vizly1go84zg60s2j30z40p1q4y.jpg)

可以看到此时的1.jsp恶意代码已经成功上传了，我们可以进行命令执行或者直接上传webshell使用蚁剑链接等操作

#### 二、CVE-2020-1938	

##### 漏洞原理：

​		Tomcat默认开启的AJP协议服务（8009端口），但该协议代码没有对用户的输入进行严格的审查与过滤导致了攻击者能够构造恶意的请求数据包造成文件包含、远程代码执行等漏洞。

##### 受影响版本：

apache tomcat 6

apache tomcat 7 < 7.0.100

apache tomcat 8 < 8.5.51

apache tomcat 9 < 9.0.31

AJP官网对AJP协议的描述：

![1](//tvax3.sinaimg.cn/large/006O5vizly1go8v0k67ikj319l0k9424.jpg)

漏洞环境：

docker+vulhub   Apache Tomcat/9.0.30

![2](//tvax4.sinaimg.cn/large/006O5vizly1go8v4zycq8j30wc0gwtd3.jpg)

这里用到了GitHub上大佬的某CVE-2020-1938的一个验证工具

[](https://github.com/hypn0s/AJPy)

下载解压后命令行输入

```
$ python tomcat.py read_file --webapp=manager /WEB-INF/web.xml +IP地址
```

![5](//tva3.sinaimg.cn/large/006O5vizly1go95k7v5yoj30pt0bb40w.jpg)

可以看到我们已经成功的把/WEB-INF/下的web.xml成功读取了出来

修复建议：

​		升级Apache Tomcat

​		关闭AJP默认开启端口（8009）

#### 三、Tomcat8

##### war后门文件部署：

**1、漏洞简介及成因**

Tomcat 支持在后台部署war文件，可以直接将webshell部署到web目录下。

若后台管理页面存在弱口令，则可以通过爆破获取密码。

开启漏洞环境，这里直接使用弱口令tomcat/tomcat登录后台

![6](//tvax1.sinaimg.cn/large/006O5vizly1go961mtu70j31hc0smae9.jpg)

在该页面就可以直接上传jsp木马，具体操作就不再演示了

修改建议：

​		·	增加管理员口令强度

​		·	增强用户身份验证，遵循最小权限原则为tomcat应用分配系统账号权限