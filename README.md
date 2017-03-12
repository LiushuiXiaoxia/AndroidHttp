# Android Http网络开发神兵利器

---

[TOC]

本人在Android移动端开发已经有不少年的经验，经历颇多，感受颇深，特写下此文记录下在Android Http网络开发中的各种工具使用，如果有其他想法和建议，欢迎前来交流 xiaqiulei@126.com 。 本人文笔较差，请不要见怪。

Android开发的时候，很多APP都是基于网络请求的，大部分应用都是基于http的，少部分是基于Socket的，本文主要介绍Android Http开发的主要实践和工具。

本文最先发表于[Github](https://github.com/LiushuiXiaoxia/AndroidHttp)，如有转载，请注明转载出处。

# Http协议

说起Http网络请求，那么要介绍下Http协议，我在公司面试的时候也很喜欢问这个问题。很多做好了3-5年的工程师，竟然不知道Http协议，只知道怎么调用类库，不知道原理。

作为一个有追求的程序员，Http协议还是需要了解的。知其然知其所以然，在了解Http协议的情况下，才能在实际开发中灵活运用和优化。

这里有篇 [文章](http://www.cnblogs.com/ranyonsue/p/5984001.html) ，Http协议介绍的很详细。

## HTTP简介

HTTP协议是Hyper Text Transfer Protocol（超文本传输协议）的缩写,是用于从万维网（WWW:World Wide Web ）服务器传输超文本到本地浏览器的传送协议。

HTTP是一个基于TCP/IP通信协议来传递数据（HTML 文件, 图片文件, 查询结果等）。

HTTP是一个属于应用层的面向对象的协议，由于其简捷、快速的方式，适用于分布式超媒体信息系统。它于1990年提出，经过几年的使用与发展，得到不断地完善和扩展。目前在WWW中使用的是HTTP/1.0的第六版，HTTP/1.1的规范化工作正在进行之中，而且HTTP-NG(Next Generation of HTTP)的建议已经提出。

HTTP协议工作于客户端-服务端架构为上。浏览器作为HTTP客户端通过URL向HTTP服务端即WEB服务器发送所有请求。Web服务器根据接收到的请求后，向客户端发送响应信息。

![](https://raw.githubusercontent.com/LiushuiXiaoxia/AndroidHttp/master/doc/http_request_response.jpg)

## 主要特点

1、简单快速：客户向服务器请求服务时，只需传送请求方法和路径。请求方法常用的有GET、HEAD、POST。每种方法规定了客户与服务器联系的类型不同。由于HTTP协议简单，使得HTTP服务器的程序规模小，因而通信速度很快。

2、灵活：HTTP允许传输任意类型的数据对象。正在传输的类型由Content-Type加以标记。

3.无连接：无连接的含义是限制每次连接只处理一个请求。服务器处理完客户的请求，并收到客户的应答后，即断开连接。采用这种方式可以节省传输时间。

4.无状态：HTTP协议是无状态协议。无状态是指协议对于事务处理没有记忆能力。缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能导致每次连接传送的数据量增大。另一方面，在服务器不需要先前信息时它的应答就较快。

5、支持B/S及C/S模式。

## Request

![Http请求消息结构](https://raw.githubusercontent.com/LiushuiXiaoxia/AndroidHttp/master/doc/http_request.png)

上图是一个Http请求消息结构，我们可以使用抓包工具抓取网络请求,因为是Get请求，所以后面没有请求数据

```
GET /562f25980001b1b106000338.jpg HTTP/1.1
Host    img.mukewang.com
User-Agent    Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.106 Safari/537.36
Accept    image/webp,image/*,*/*;q=0.8
Referer    http://www.imooc.com/
Accept-Encoding    gzip, deflate, sdch
Accept-Language    zh-CN,zh;q=0.8
```

第一部分：请求行，用来说明请求类型,要访问的资源以及所使用的HTTP版本.

GET说明请求类型为GET,[/562f25980001b1b106000338.jpg]为要访问的资源，该行的最后一部分说明使用的是HTTP1.1版本。

第二部分：请求头部，紧接着请求行（即第一行）之后的部分，用来说明服务器要使用的附加信息

从第二行起为请求头部，HOST将指出请求的目的地.User-Agent,服务器端和客户端脚本都能访问它,它是浏览器类型检测逻辑的重要基础.该信息由你的浏览器来定义,并且在每个请求中自动发送等等

第三部分：空行，请求头部后面的空行是必须的

即使第四部分的请求数据为空，也必须有空行。

第四部分：请求数据也叫主体，可以添加任意的其他数据。

这个例子的请求数据为空。

下面这个例子是Post方式。

```
POST / HTTP1.1
Host:www.wrox.com
User-Agent:Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; .NET CLR 2.0.50727; .NET CLR 3.0.04506.648; .NET CLR 3.5.21022)
Content-Type:application/x-www-form-urlencoded
Content-Length:40
Connection: Keep-Alive

name=Professional%20Ajax&publisher=Wiley
```

第一部分：请求行，第一行明了是post请求，以及http1.1版本。

第二部分：请求头部，第二行至第六行。

第三部分：空行，第七行的空行。

第四部分：请求数据，第八行。

## Response

一般情况下，服务器接收并处理客户端发过来的请求后会返回一个HTTP的响应消息。

HTTP响应也由四个部分组成，分别是：状态行、消息报头、空行和响应正文。

![Http响应消息结构](https://raw.githubusercontent.com/LiushuiXiaoxia/AndroidHttp/master/doc/http_reponse.jpg)


```
HTTP/1.1 200 OK
Date: Fri, 22 May 2009 06:07:21 GMT
Content-Type: text/html; charset=UTF-8

<html>
      <head></head>
      <body>
            <!--body goes here-->
      </body>
</html>
```

第一部分：状态行，由HTTP协议版本号， 状态码， 状态消息 三部分组成。

第一行为状态行，（HTTP/1.1）表明HTTP版本为1.1版本，状态码为200，状态消息为（ok）

第二部分：消息报头，用来说明客户端要使用的一些附加信息

第二行和第三行为消息报头，
Date:生成响应的日期和时间；Content-Type:指定了MIME类型的HTML(text/html),编码类型是UTF-8

第三部分：空行，消息报头后面的空行是必须的

第四部分：响应正文，服务器返回给客户端的文本信息。

空行后面的html部分为响应正文。

## Code

状态代码有三位数字组成，第一个数字定义了响应的类别，共分五种类别:

1xx：指示信息--表示请求已接收，继续处理

2xx：成功--表示请求已被成功接收、理解、接受

3xx：重定向--要完成请求必须进行更进一步的操作

4xx：客户端错误--请求有语法错误或请求无法实现

5xx：服务器端错误--服务器未能实现合法的请求

常见状态码：

```
200 OK                        // 客户端请求成功
400 Bad Request               // 客户端请求有语法错误，不能被服务器所理解
401 Unauthorized              // 请求未经授权，这个状态代码必须和WWW-Authenticate报头域一起使用
403 Forbidden                 // 服务器收到请求，但是拒绝提供服务
404 Not Found                 // 请求资源不存在，eg：输入了错误的URL
500 Internal Server Error     // 服务器发生不可预期的错误
503 Server Unavailable        // 服务器当前不能处理客户端的请求，一段时间后可能恢复正常
```

## Method

根据HTTP标准，HTTP请求可以使用多种请求方法。
HTTP1.0定义了三种请求方法： GET, POST 和 HEAD方法。
HTTP1.1新增了五种请求方法：OPTIONS, PUT, DELETE, TRACE 和 CONNECT 方法。

```
GET     请求指定的页面信息，并返回实体主体。
HEAD     类似于get请求，只不过返回的响应中没有具体的内容，用于获取报头
POST     向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST请求可能会导致新的资源的建立和/或已有资源的修改。
PUT     从客户端向服务器传送的数据取代指定的文档的内容。
DELETE      请求服务器删除指定的页面。
CONNECT     HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器。
OPTIONS     允许客户端查看服务器的性能。
TRACE     回显服务器收到的请求，主要用于测试或诊断。
```

## Header

HTTP消息由客户端到服务器的请求和服务器到客户端的响应组成。请求消息和响应消息都是由开始行（对于请求消息，开始行就是请求行，对于响应消息，开始行就是状态行），消息报头（可选），空行（只有CRLF的行），消息正文（可选）组成。

HTTP消息报头包括普通报头、请求报头、响应报头、实体报头。
每一个报头域都是由名字+“：”+空格+值 组成，消息报头域的名字是大小写无关的。

# 网络请求库

下面介绍几个常见的Android http网路开发的工具库。

## URLConnection

URLConnection是Java JDK中自带的网络请求类，使用的场景不多，因为使用比较繁琐，直接使用底层IO流进行开发，需要了解Http协议才能很好的使用，很多时候需要自己封装，不方便。一般的一些提供第三方服务的sdk会使用，一是为了保证sdk体积大小，二是减少不必要的第三方库引入。

这里不做详细介绍， [这里有一篇博可以详细介绍了](http://www.blogjava.net/supercrsky/articles/247449.html) 。

## Apache HttpClient

HttpClient是Apache Jakarta Common下的子项目，用来提供高效的、最新的、功能丰富的支持HTTP协议的客户端编程工具包，并且它支持HTTP协议最新的版本和建议。HttpClient已经应用在很多的项目中，比如Apache Jakarta上很著名的另外两个开源项目Cactus和HTMLUnit都使用了HttpClient。

HttpClient相比传统JDK自带的URLConnection，增加了易用性和灵活性，它不仅是客户端发送Http请求变得容易，而且也方便了开发人员测试接口（基于Http协议的），即提高了开发的效率，也方便提高代码的健壮性。


Android在低版本的系统Api中也有Httpclient相关类，但是后来就已经废弃,相关资料可以 [参考这里](https://developer.android.com/reference/org/apache/http/client/package-summary.html).

[HttpClient相关博客](http://blog.csdn.net/wangpeng047/article/details/19624529)

## Volley

[Android Volley](https://developer.android.com/training/volley/index.html) 是在2013年Google I/O大会上推出了一个新的网络通信框架——Volley。Volley可是说是把AsyncHttpClient和Universal-Image-Loader的优点集于了一身，既可以像AsyncHttpClient一样非常简单地进行HTTP通信，也可以像Universal-Image-Loader一样轻松加载网络上的图片。除了简单易用之外，Volley在性能方面也进行了大幅度的调整，它的设计目标就是非常适合去进行数据量不大，但通信频繁的网络操作，而对于大数据量的网络操作，比如说下载文件等，Volley的表现就会非常糟糕。

有 [资料](http://blog.csdn.net/guolin_blog/article/details/12452307) 指出Volley在进行网络请求时，在Android 2.3及以上版本，使用的是HttpURLConnection，而在Android 2.2及以下版本，使用的是HttpClient。主要是因为HttpURLConnection存在相关bug。

[Volley相关资料](http://blog.csdn.net/guolin_blog/article/details/17482095)

[Volley相关资料](http://blog.csdn.net/guolin_blog/article/details/12452307)

## OkHttp & Retrofit

OkHttpSquare公司开源的处理网络请求的开源项目,是安卓端最火热的轻量级框架，用于替代HttpUrlConnection和Apache HttpClient。最新的版本是OkHttp3，Github地址在 [这里](https://github.com/square/okhttp) 。

Retrofit也是是Square公司开发的一款针对Android网络请求的框架，Retrofit2底层基于OkHttp实现的，OkHttp现在已经得到Google官方认可，大量的app都采用OkHttp做网络请求， [Github地址](https://github.com/square/retrofit) 。

网络上也有很多关于OkHttp、Retrofit的示例和资料，而且是目前Android移动端最流行的框架，还是很值得了解一下，在这里就不做详细介绍。

# 工具

## Charles

Charles是一个HTTP代理服务器,HTTP监视器,反转代理服务器·它允许一个开发者查看所有连接互联网的HTTP通信·这些包括request、response、headers（包含cookies与caching信息），由于笔者的开发机器是Mac，所以只介绍了Charles，Windows开发环境的同学可以使用fiddler。

[Charles介绍](http://blog.devtang.com/2015/11/14/charles-introduction/)

Charles 主要的功能包括：

> 截取 Http 和 Https 网络封包。

> 支持网络请求调试，设置断点，方便调试

> 支持重发网络请求，方便后端调试。

> 支持修改网络请求参数。

> 支持网络请求的截获并动态修改。

> 支持模拟弱网。


上面介绍了，可以使用Charles方便设置断点，方便调试，可以很容易的修改数据包，这样可以动态修改数据，mock api接口，在实际开发过程中很有用。

[Charles Mock 数据](http://blog.csdn.net/guijiaoba/article/details/43198503)


## PostMan & Paw

上面介绍了网络抓包工具Charles，先介绍2款模拟http请求的软件。

Postman是一款功能强大的网页调试与发送网页HTTP请求的Chrome插件,使用很简单。

下图模拟发送知乎Api的网络请求。

`http://news-at.zhihu.com/api/4/themes`

![](https://raw.githubusercontent.com/LiushuiXiaoxia/AndroidHttp/master/doc/post_man.png)

[Postman官网](https://www.getpostman.com/)

[Postman资料](http://chromecj.com/web-development/2014-09/60.html)

Paw 也是类似于Postman的一款模拟http请求的软件，只不过postman依赖于chrome，经常需要点击2此才可以打开postman，所以个人更加喜欢paw，下图是paw请求知乎api的示例。BTW, paw是一款收费的软件，不过么网上也有破解的，在这里就不介绍如何获取了，反正大家都懂的。

![](https://raw.githubusercontent.com/LiushuiXiaoxia/AndroidHttp/master/doc/paw.png)

[Paw 官网](https://paw.cloud/)

[Paw 使用教程](https://yq.aliyun.com/articles/29772)

## Chrome Dev Tools

Chrome dev tools是Google Chrome浏览器中自带一块调试工具。如果你是做软件开发的，基本都了解过。

虽然Chrome dev tools 在web开发中使用很多，但是在移动端中也是有用到的，这个后面再介绍。

![](https://raw.githubusercontent.com/LiushuiXiaoxia/AndroidHttp/master/doc/chrome_dev_tools.png)

[Chrome dev tools 相关资料](https://developer.chrome.com/devtools)

[Chrome dev tools 相关资料](https://github.com/GoogleChrome/devtools-docs)

[Chrome dev tools 相关资料](https://segmentfault.com/a/1190000000683599)

## curl

curl也是和上面poastman与paw功能类似的，不过没有和上面两款放在一起讲是因为curl比较重要，而且具有统一标准性与方便性。

curl是利用URL语法在命令行方式下工作的开源文件传输工具。它被广泛应用在Unix、多种Linux发行版中，并且有DOS和Win32、Win64下的移植版本。

还是原先的例子，请求知乎的api。

![](https://raw.githubusercontent.com/LiushuiXiaoxia/AndroidHttp/master/doc/curl.png)

很简单在curl命令直接加入url即可发送网络请求。

为什么有postman和paw这么友好的软件后，还需要介绍的curl呢，因为curl是一个命令，是文本，是字符串，当你其他同事开发联调的时候，直接通过im工具发送即可，这样网络请求就能很容移动复现。很多的上面介绍的charles、paw、google dev tool都支持把某一条请求导出为curl命令。这就相当于curl是一个标准，可以在不同机器上面复用，所以了解和使用curl是很有必要的。比如联调开发时候，服务器某个api调试不通过，需要与服务器同学沟通与复习，你直接丢一个curl语句即可，如果对方很懂，那么会觉的你很专业，如果对方不懂，顺便可以装个逼。

[curl 资料](http://www.cnblogs.com/gbyukg/p/3326825.html)

[curl 官网](https://curl.haxx.se/)


# OkHttp相关

前面介绍了OkHttp，下面介绍几个基于OkHttp拦截器机制衍生出的常用的工具。

## logging-interceptor

开发中经常需要对网络请求进行查看，抓包是可以的，不过现在很多app都是全栈https，想要抓包就没有那么容易了，那么最简单的还是打日志。

`logging-interceptor`就是干这个事的，在初始化okhttpclient的时候，添加下这个拦截器，那么所有的网络请求会被记录下来。

[相关资料](https://github.com/square/okhttp/wiki/Interceptors)

[相关资料](https://github.com/ihsanbal/LoggingInterceptor)

## Stetho

Stetho是Facebook开源的一个工具，前面说过Google Chrome dev tools，那么Stetho可以让Chrome调试Android，监控网络请求。

同时Stetho还支持查看数据库，SharePreference等，而这个前提是必须root，查看view树，使用Stetho也可以做到。

网上也有很多也有很多相关资料，这里不介绍详细使用。

[stetho github](https://github.com/facebook/stetho)

[stetho 相关资料](http://www.jianshu.com/p/03da9f91f41f)

## chuck

上面介绍的两款工具，一种是需要在logcat中查看网络请求数据，一种是需要在chrome中查看数据，有没有办法不需要借助任何第三方工具，直接查看呢，答案是肯定的。chuck是我最近关注比较高的一个开源工具，而且很快的就在项目中使用了。

下面官网的截图，可以先了解下chuck的使用效果。

![](https://raw.githubusercontent.com/LiushuiXiaoxia/AndroidHttp/master/doc/chuck.gif)

在高版本的Android中，还支持多屏显示，那么一边操作一边显示。

![](https://raw.githubusercontent.com/LiushuiXiaoxia/AndroidHttp/master/doc/multiwindow.gif)

集成的方法都很简单，只需要在初始化OkHttpClient的时候，添加对应的拦截器即可。

Gradle依赖

```gradle
dependencies {
   debugCompile 'com.readystatesoftware.chuck:library:1.0.4'
   releaseCompile 'com.readystatesoftware.chuck:library-no-op:1.0.4'
 }
```

添加拦截器

```java
OkHttpClient client = new OkHttpClient.Builder()
  .addInterceptor(new ChuckInterceptor(context))
  .build();
```

[Chuck github](https://github.com/jgilfelt/chuck)

# 其他

## RESTful

RESTful一种软件架构风格，设计风格而不是标准，只是提供了一组设计原则和约束条件。它主要用于客户端和服务器交互类的软件。基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制。

REST（英文：Representational State Transfer，简称REST）描述了一个架构样式的网络系统，比如 web 应用程序。它首次出现在 2000 年 Roy Fielding 的博士论文中，他是 HTTP 规范的主要编写者之一。在目前主流的三种Web服务交互方案中，REST相比于SOAP（Simple Object Access protocol，简单对象访问协议）以及XML-RPC更加简单明了，无论是对URL的处理还是对Payload的编码，REST都倾向于用更加简单轻量的方法设计和实现。值得注意的是REST并没有一个明确的标准，而更像是一种设计的风格。

虽然本文的主题是介绍Android Http开发的相关内容，并且RESTful是服务端的一种概念。但是我觉的，还是有很必要了解的，最起码可以作为判断你的服务端小伙伴给的接口是否满足RESTful的标准的依据。

后面贴一篇[阮一峰大神的博客介绍RESTful](http://www.ruanyifeng.com/blog/2011/09/restful)

# 总结

## 心得

上面介绍了很多种关于网络请求的工具，具体可以分为这几类。

HTTP接口调用工具，这类工具主要是模拟客户端，发起网络请求，方便调试服务器接口。

* postman
* paw
* curl

抓包工具，这类工具不光适合Android开发，也适合其他网络开发，工具使用简单，不过在使用前需要做少许代理配置。WiresShark是一款更加强大的工具，使用比较复杂，而且比较底层，所以在本文中就没介绍，有兴趣的同学可以自己查阅相关资料。

* Charles
* Fiddler
* WiresShark

Android Http请求工具库，这类工具类变化很快，经常会过个几年就会有新的一种工具替换，但是本质思想还是一样的，都是基于URLConnection和HTTP做了一次封装调用。

* URLConnection
* HttpClient
* Volley
* OkHttp

OkHttp相关，这类工具都是基于OkHttp做了二次封装开发，这个主要归功于OkHttp强大开发模式和架构，开发人员可以方便对OkHttp进行自定义和二次开发。工具很强大，不过也有局限性，只能依附于OkHttp的框架，不利于扩展到其他平台和工具。

* logging-interceptor
* Stetho
* Chuck

## 工具对比

上面介绍好几款可以监控App网络请求的工具，那么如何根据实际情况，选择一款适合自己项目和团队的工具呢？


### Charles & Fiddler

这类工具不依赖与平台和App开发的工具库，不管你是Android、还是iOS，不管使用的是Volley，还是OkHttp都是可以监控的，同时还支持HTTP的调试，方便mock数据，简直是神器。

不过也有少许缺点，比如是不支持Https，如果你的app使用的是https接口，那么这类工具一般情况是不能使用的，不过也有办法，比如把charles的证书设置为根证书，则可以检测到。但是也是有局限的，如果app设置强制校验https证书，此类方法还是不可行。

因为平台无光，可检测到某个手机设备上面的全部网络请求，数据量有时候会很大，如果经常调试某一个app，那么域名一般是固定的，可进行相对应的过滤。

### OkHttp相关

通过标题可以看成，此类工具必须依附于OKHttp，那么只能满足Android平台并且App的网络请求库是OkHttp。上面介绍的三款工具，可以选择性的选择其中几种或者是全部，暂时还没有发现冲突的地方。因为是直接嵌入到代码中，那么Https也是可以监控到。同时也只能监控当前设备上的当前应用，不可以检测其他的app。

不过在发布线上或者正式颁布时，需要手动去除这些代码，否则会有很大麻烦，同时也会把相关的隐私和安全反馈给使用的用户。

好了，上面就是关于App网络情况的监控工具比较，建议两个大分类都可以几个或者多个配合使用，具体问题在根据具体工具使用，方便问题解决与网络监控。

## 其他建议

上面介绍的OkHttp的相关的工具，都是需要在初始化OkHttpClient的时候，手动添加对应的拦截器，如果忘记添加则不会产生效果。

那么可以使用现在流行的Aop技术，在编译时候，会在所有调用OkHttpClient的地方，自动添加，保证不会忘记，如果你的App还是用其他的sdk，sdk里面也是用OKhttp的话，也会监控到sdk里面的网络请求，不少大厂已经使用了这种方法，然后收集请求数据到后端，方便管理和优化。

下面简单介绍相关代码:

首先集成`gradle_plugin_android_aspectjx`插件

```gradle
dependencies {
    classpath 'com.hujiang.aspectjx:gradle-android-plugin-aspectjx:1.0.9'
}
```

然后定义Aspectj切入点

```java

import com.facebook.stetho.okhttp3.StethoInterceptor;
import com.readystatesoftware.chuck.ChuckInterceptor;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;

import me.ele.breakfastapp.AppContext;
import okhttp3.OkHttpClient;

@Aspect
public class OkHttpAspect {

    @Pointcut("call(public okhttp3.OkHttpClient build())")
    public void build() {
    }

    @Around("build()")
    public Object aroundBuild(ProceedingJoinPoint joinPoint) throws Throwable {
        Object target = joinPoint.getTarget();
        if (target instanceof OkHttpClient.Builder) {
            OkHttpClient.Builder builder = (OkHttpClient.Builder) target;
            builder.addNetworkInterceptor(new StethoInterceptor());
            builder.addNetworkInterceptor(new ChuckInterceptor(AppContext.get()));
        }

        return joinPoint.proceed();
    }
}
```

那么在代码编译的时候，会自动插入字节码。

Aop相关资料:

[Aop介绍](http://blog.csdn.net/innost/article/details/49387395)

[Aop资料](http://blog.csdn.net/crazy__chen/article/details/52014672)

[Assectj](http://www.jianshu.com/p/bb9343e8af17)

[Assectj Gradle 插件](https://github.com/HujiangTechnology/gradle_plugin_android_aspectjx)

# 参考资料

[Http博客](http://www.cnblogs.com/ranyonsue/p/5984001.html)

[Http博客](http://www.cnblogs.com/li0803/archive/2008/11/03/1324746.html)

[URLConnection](http://www.blogjava.net/supercrsky/articles/247449.html)

[Android HttpClient](https://developer.android.com/reference/org/apache/http/client/package-summary.html)

[HttpClient相关博客](http://blog.csdn.net/wangpeng047/article/details/19624529)

[Volley相关资料](http://blog.csdn.net/guolin_blog/article/details/17482095)

[Volley相关资料](http://blog.csdn.net/guolin_blog/article/details/12452307)

[OkHttp](https://github.com/square/okhttp)

[Retrofit Github地址](https://github.com/square/retrofit)

[Charles介绍](http://blog.devtang.com/2015/11/14/charles-introduction/)

[Charles Mock 数据](http://blog.csdn.net/guijiaoba/article/details/43198503)

[Postman官网](https://www.getpostman.com/)

[Postman资料](http://chromecj.com/web-development/2014-09/60.html)

[Paw 官网](https://paw.cloud/)

[Paw 使用教程](https://yq.aliyun.com/articles/29772)

[Chrome dev tools 相关资料](https://developer.chrome.com/devtools)

[Chrome dev tools 相关资料](https://github.com/GoogleChrome/devtools-docs)

[Chrome dev tools 相关资料](https://segmentfault.com/a/1190000000683599)

[curl 资料](http://www.cnblogs.com/gbyukg/p/3326825.html)

[curl 官网](https://curl.haxx.se/)

[Interceptors相关资料](https://github.com/square/okhttp/wiki/Interceptors)

[LoggingInterceptors相关资料](https://github.com/ihsanbal/LoggingInterceptor)

[stetho github](https://github.com/facebook/stetho)

[stetho 相关资料](http://www.jianshu.com/p/03da9f91f41f)

[Chuck github](https://github.com/jgilfelt/chuck)

[RESTful](http://www.ruanyifeng.com/blog/2011/09/restful)

[Aop介绍](http://blog.csdn.net/innost/article/details/49387395)

[Aop资料](http://blog.csdn.net/crazy__chen/article/details/52014672)

[Assectj](http://www.jianshu.com/p/bb9343e8af17)

[Assectj Gradle 插件](https://github.com/HujiangTechnology/gradle_plugin_android_aspectjx)