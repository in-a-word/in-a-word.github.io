# DVWA系列之---文件上传File Upload

#### 概述：

​		文件上传漏洞是指开发人员对上传的文件没有进行严格的验证和过滤，导致了一些恶意用户能够通过构造某些特定的方法向服务端上传恶意的文件包括不限于木马，病毒，恶意脚本等，恶意文件上传1后会进一步利用服务端的处理逻辑或者解析漏洞进一步造成更严重的后果

一、low级别

源码分析

![l](https://tvax1.sinaimg.cn/large/006O5vizly1gkuuwe4ptmj30r30ckaao.jpg)

可以看到low级别的一贯风格👀---没有一丝丝防备

想传什么文件就传什么文件，管你是jpg、png、抑或是php、什么的

![4.1](https://tva2.sinaimg.cn/large/006O5vizly1gkuv0dkotoj30lk09mmxk.jpg)



二、medium级别

源码分析：

![m](https://tva1.sinaimg.cn/large/006O5vizly1gkuv14mtmwj30zk0kx75o.jpg)

可以看到，medium里加上了三个获取上传文件的三个信息，分别为name、type、size、以及一个文件类型的检查，但是！！！

这个文件类型检查仅仅是是前端的，我们可以通过抓包然后修改文件的类型content-type参数然后就能绕过了

![4.2](https://tva2.sinaimg.cn/large/006O5vizly1gkvdhgpmh2j31hc0smmzz.jpg)

修改完后我们发送过去看看结果

![4.3](https://tva3.sinaimg.cn/large/006O5vizly1gkvdhgvdpaj30hu06egln.jpg)

可以看到我们的1.php已经被成功发送了，由此不难发现，仅仅是基于前端的检查是不够的，其安全性也是微乎其微的

三、high级别

源码分析：

![h](https://tva2.sinaimg.cn/large/006O5vizly1gkvdlaoadij314q0lrtaa.jpg)

从源码不难看出，较于medium它除了获取上传文件的几个参数外还进行了一个if判断，这次就不同于medium的仅仅是前端的一个文件类型检查了而是在服务端的检查，其读取文件名后的“ . ”以及其后缀，用以判断上传的文件是否为jpg、jpeg、png、三者之一，然后还有一个文件大小的限制，由于这次的上传文件检测是在服务端，简单的前端绕过已经不能奏效了，我们可以把一些恶意文件或者，木马等写在一张真正图片中然后进行上传，可以利用工具写入，我这里直接用cmd的copy命令进行文件改写

![cmd](https://tva1.sinaimg.cn/large/006O5vizly1gkvf6bewdpj30f505h0sp.jpg)

记事本打开图片查看

![4.4](https://tvax4.sinaimg.cn/large/006O5vizly1gkvf794jg4j317r0ji794.jpg)

可以看到一句话已经插入到末尾了

把这个插入一句话的图片上传试试看

![4.5](https://tvax1.sinaimg.cn/large/006O5vizly1gkvfchzwp9j30nb09m0t7.jpg)

可以看到已经上传成功了

![i](https://tva2.sinaimg.cn/large/006O5vizly1gkvgmdsvcuj31hc0smadc.jpg)

上面是impossible的源码，目前看来找不到太好的方法去绕过

总结：

文件上传，不仅仅是上传完了就算了，文件上传本意上传了一些恶意脚本，木马然后进行下一步的漏洞利用，进而达到文件泄露，getshell等目的