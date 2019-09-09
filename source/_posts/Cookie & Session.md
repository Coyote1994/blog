---
title: Cookie & Session
date: 2018-09-27 14:32:57
categories: 
- 计算机网络
tags:
- Session
- Cookie
---

## 前言

HTTP协议是一种无状态协议，在数据交换完毕后，服务器端和客户端的链接就会关闭，每次交换数据都需要建立新的链接。此时，服务器无法从链接上跟踪会话。

为了分辨链接是谁发起的，需自己去解决这个问题，不然有些情况下即使是同一个网站每打开一个页面也都要登录一下。而Session和Cookie就是为解决这个问题而提出来的两个机制。

## Cookie

Cookies是服务器在本地机器上存储的小段文本并随每一个请求发送至同一服务器，是在客户端保持状态的方案。

Cookie分为二种：

1. 以文件方式存在硬盘空间上的长期性的Cookie
2. 停留在浏览器所占内存中的临时性的Cookie

### Cookie工作流程

首先，服务器端在响应中利用Set-Cookie header来创建一个Cookie ，然后，浏览器在它的请求中通过Cookie header包含这个已经创建的Cookie，并且把它返回至服务器，从而完成浏览器的论证。

### Cookies格式

#### Netscape公司规定的Cookie格式

```
Set-Cookie: NAME=VALUE;Expires=DATE;Path=PATH;Domain=DOMAIN_NAME;SECURE
```

* NAME = VALUE

这是每一个Cookie均必须有的部分。NAME是该Cookie的名称，VALUE是该Cookie的值。在字符串“NAME=VALUE”中，不含分号、逗号和空格等字符。

* Expires = DATE

Expires变量是一个只写变量，它确定了Cookie有效终止日期。该属性值DATE必须以特定的格式来书写：星期几,DD-MM-YY HH:MM:SS GMT，GMT表示这是格林尼治时间。反之，不以这样的格式来书写，系统将无法识别。该变量可省，如果缺省时，则Cookie的属性值不会保存在用户的硬盘中，而仅仅保存在内存当中，Cookie文件将随着浏览器的关闭而自动消失。
 
* Domain = DOMAIN-NAME

Domain该变量是一个只写变量，它确定了哪些Internet域中的Web服务器可读取浏览器所存取的Cookie，即只有来自这个域的页面才可以使用Cookie中的信息。这项设置是可选的，如果缺省时，设置Cookie的属性值为该Web服务器的域名。

* Path = PATH

Path属性定义了Web服务器上哪些路径下的页面可获取服务器设置的Cookie。一般如果用户输入的URL中的路径部分从第一个字符开始包含Path属性所定义的字符串，浏览器就认为通过检查。如果Path属性的值为“/”，则Web服务器上所有的WWW资源均可读取该Cookie。同样该项设置是可选的，如果缺省时，则Path的属性值为Web服务器传给浏览器的资源的路径名。可以看出我们借助对Domain和Path两个变量的设置，即可有效地控制Cookie文件被访问的范围。

* Secure

在Cookie中标记该变量，表明只有当浏览器和Web Server之间的通信协议为加密认证协议时，浏览器才向服务器提交相应的Cookie。当前这种协议只有一种，即为HTTPS。

#### 在RFC2109中定义的SET-COOKIE响应报头的格式

```
Set-Cookie: Name = Value; Comment = value; Domain = value; Max-Age = value; Path = Value; Secure; Version = 1 * DIGIT;
```


* Name = Value 属性值对必须首先出现，在此之后的属性-值对可以以任何顺序出现。

* Comment 属性是可选的，因为Cookie可能包含其它有关用户私有的信息。这个属性允许服务器说明这个Cookie的使用，用户可以检查这个消息，然话决定是否加入或继续会话。

* Max-Age 属性是可选的，用于定义Cookie的生存时间，以秒为单位。如果超过了这个时间，客户端就应该丢弃这个cookie。如果指定的秒数为0，表示这个cookie应立即被丢弃。

### Cookie的安全性

Cookie在客户端存储，可以编辑伪造，是十分不安全的，为了提高安全性，我们需要做如下处理：

* 对保存到cookie里面的敏感信息必须加密

* 设置HttpOnly为true
	* 该属性值的作用就是防止Cookie值被页面脚本读
	* 但是设置HttpOnly属性，HttpOnly属性只是增加了攻击者的难度，Cookie盗窃的威胁并没有彻底消除，因为cookie还是有可能传递的过程中被监听捕获后信息泄漏

* 设置Secure为true
	* 给Cookie设置该属性时，只有在https协议下访问的时候，浏览器才会发送该Cookie
	* 把cookie设置为secure，只保证cookie与WEB服务器之间的数据传输过程加密，而保存在本地的cookie文件并不加密。如果想让本地cookie也加密，得自己加密数据

* 给Cookie设置有效期
	* 如果不设置有效期，万一用户获取到用户的Cookie后，就可以一直使用用户身份登录
	* 在设置Cookie认证的时候，需要加入两个时间，一个是“即使一直在活动，也要失效”的时间，一个是“长时间不活动的失效时间”，并在Web应用中，首先判断两个时间是否已超时，再执行其他操作
	
## Session

* Session通过在服务器端记录信息确定用户身份。

* 存在服务器的一种用来存放用户数据的类HashTable结构。

* 浏览器第一次发送请求时，服务器自动生成了一HashTable和一Session ID来唯一标识这个HashTable，并将其通过响应发送到浏览器。浏览器第二次发送请求会将前一次服务器响应中的Session ID放在请求中一并发送到服务器上，服务器从请求中提取出Session ID，并和保存的所有Session ID进行对比，找到这个用户对应的HashTable。一般这个值会有个时间限制，超时后毁掉这个值，默认30分钟。

* 当用户在应用程序的 Web页间跳转时，存储在 Session 对象中的变量不会丢失而是在整个用户会话中一直存在下去。

* Session是基于Cookie技术实现。建立一个连接就生成一个session id，打开几个页面就好几个了，这里就用到了Cookie，把session id存在Cookie中，每次访问的时候将Session id带过去就可以识别了。

* 存储数据量方面：session 能够存储任意的 java 对象。

* Session过多时会消耗服务器资源，大型网站会有专门Session服务器。

* Session相比Cookie较为安全

## 单点登录

单点登录的原理是后端生成一个 session ID，设置到 cookie，后面所有请求浏览器都会带上cookie，然后服务端从cookie获取 session ID，查询到用户信息。所以，保持登录的关键不是cookie，而是通过cookie 保存和传输的 session ID，本质是能获取用户信息的数据。除了cookie，还常用 HTTP 请求头来传输。但这个请求头浏览器不会像cookie一样自动携带，需手工处理。

---

> 简要说明Cookie和Session的区别：
> Cookie和Session都是会话技术，Cookie是运行在客户端，Session是运行在服务器端
> Cookie有大小限制以及浏览器在存cookie的个数也有限制，Session是没有大小限制和服务器的内存大小有关
> Cookie有安全隐患，通过拦截或本地文件找得到你的cookie后可以进行攻击
> Session是保存在服务器端上会存在一段时间才会消失，如果session过多会增加服务器的压力








