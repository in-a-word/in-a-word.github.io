# DVWA系列之---暴力破解（Brute Force）全等级教程

​		1、何为暴力破解？

通过利用大量猜测和穷举的方式来尝试获取用户口令的攻击方式，又名为枚举法。

2、常用工具---burp suite、hydra

​    本次我用的是burp suite里的intruder模块

3、low等级

源码：

```
 <?php

if( isset( $_GET[ 'Login' ] ) ) {
    // Get username
    $user = $_GET[ 'username' ];

    // Get password
    $pass = $_GET[ 'password' ];
    $pass = md5( $pass );

    // Check the database
    $query  = "SELECT * FROM `users` WHERE user = '$user' AND password = '$pass';";
    $result = mysqli_query($GLOBALS["___mysqli_ston"],  $query ) or die( '<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );

    if( $result && mysqli_num_rows( $result ) == 1 ) {
        // Get users details
        $row    = mysqli_fetch_assoc( $result );
        $avatar = $row["avatar"];

        // Login successful
        echo "<p>Welcome to the password protected area {$user}</p>";
        echo "<img src=\"{$avatar}\" />";
    }
    else {
        // Login failed
        echo "<pre><br />Username and/or password incorrect.</pre>";
    }

    ((is_null($___mysqli_res = mysqli_close($GLOBALS["___mysqli_ston"]))) ? false : $___mysqli_res);
}

?>



```

可以看到，该等级下没有任何的防护措施，get 过来的参数没有进行任何的过滤，直接传入，所以直接burp抓包发送到intruder模块，加载字典进行爆破

需要爆破的变量设置完毕后，就可以加载字典进行爆破了，本次爆破点为username以及password

![2.1](https://tva4.sinaimg.cn/large/006O5vizly1gkukjkfqomj31ha0m0abk.jpg)

接下来就是加载字典

![2.2](https://tvax4.sinaimg.cn/large/006O5vizly1gkukjknebmj31hb0nx0ua.jpg)

点击start attack

![image](https://tvax4.sinaimg.cn/large/006O5vizly1gkukjmhomgj31uo11i7h1.jpg)

爆破结果对比上下数据包的长度，明显不同的数据包查看，显示为登陆成功

由于没有设置任何的防护，本题还有其他的方法，具体可自行探讨（SQL万能密码）

难度升级：mid

源码如下

![mid](https://tva1.sinaimg.cn/large/006O5vizly1gkukjmxt47j31gm0mm0uz.jpg)

该难度下设置了一个PHP的mysq函数---mysqli_real_escape_string

该函数会对一些特殊的字符进行转义从而对SQL注入进行一定的防护，但是仍然没有对暴力破解进行有效的防护，所以我们还是可以burp/intruder 一梭子打通

![image](https://tvax4.sinaimg.cn/large/006O5vizly1gkukjmhomgj31uo11i7h1.jpg)



有趣的是在Medium里加入了一个sleep函数，增加了破解时间

接下来说说high

![hi](https://tva4.sinaimg.cn/large/006O5vizly1gkukjmqkamj31hc0smad8.jpg)

对比上图源码不难发现，high与Medium的区别在于不仅有mysqli_real_escape_string转义特殊字符串、sleep函数，增加了破解时间，还加入了stripslashes() 函数对非法输入的处理，还多了一个token参数

抓包看看：

![2.h](https://tvax4.sinaimg.cn/large/006O5vizly1gkulbygxlgj30sc0gnq4d.jpg)

与之前不同的是，因为添加了token，所以在登陆的时候服务端会对token进行检查，这次在之前常规的爆破之上还要加上token的抓取

![2.5](https://tvax3.sinaimg.cn/large/006O5vizly1gkulmlsv32j31hc0sm42i.jpg)

选择options里的grep-extract模块，add然后选中token的值，点击🆗

然后修改一些这些地方

![2-1](https://tva4.sinaimg.cn/large/006O5vizly1gkulr5hlbij30ky0af3yx.jpg)

![2-2](https://tva2.sinaimg.cn/large/006O5vizly1gkulr5nuenj30gm06imxa.jpg)

选择字典，保存设置开始跑

![211](https://tvax3.sinaimg.cn/large/006O5vizly1gkum1hqfvoj30sp0hdwfp.jpg)

参数配置没问题的话就可以跑出来了，但是需要的时间可能会有点长，耐心等待即可



impossible级别的，，，，

![TXLYA1PB~FZX~85GSSXV`RU](https://tvax2.sinaimg.cn/large/006O5vizly1gkum7btjp3j30bt03nmx2.jpg)

添加了失败次数以及失败等待时间。。。。15分钟，防止csrf的token，以及过滤SQL注入的两个PHP函数，后面还有一个PDO

暂时还没有太好的想法