# HTTP和HTTPS

## HTTP

### 1. 简介

HTTP协议（Hyper Text Transfer Protocol，超文本传输协议）,是用于从万维网（WWW:World Wide Web ）服务器传输超文本到本地浏览器的传送协议。

HTTP基于TCP/IP通信协议来传递数据。

HTTP基于客户端/服务端（C/S）架构模型，通过一个可靠的链接来交换信息，是一个无状态的请求/响应协议。

### 2. 特点

- 无连接。限制每次连接只处理一个请求，服务器处理完成客户端请求，并收到客户端的应答后，断开连接，节省传输时间
- 媒体独立。客户端和服务器能处理的数据，都可以通过http发送。客户端以及服务器指定使用适合的MIME-type内容类型。
- 无状态。协议对事务处理没有记忆能力，比如无法记忆用户是否登录，Cookie可以解决此问题。缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能导致每次连接传送的数据量增大。另一方面，在服务器不需要先前信息时它的应答就较快。

### 		3. 消息结构

消息结构分为客户端请求消息和服务端返回消息。

#### 3.1 客户端请求消息

格式：请求行，请求头，空行和请求数据

​	*请求行：*

​	**GET /test.html HTTP/1.1** （请求方法、URL地址与Host组成完整URL、协议名称及版本号）

​	*常用消息头：*

​	**Accept:text/html,image/*** （告诉服务器，浏览器可以接收文本，网页图片）

​	**Accept-Charaset:ISO-8859-1**（接收字符的编码）

​	**Accept-Encoding:gzip,compress**（可以接收gzip和compress压缩后的数据）

​	**Accept-Language:zh-cn**（浏览器支持的语言）

​	**Connection:keep-alive**（保持连接，发完数据后，我不关闭连接）

​	**Host:https://www.baidu.com**（浏览器要访问的主机）

​	**IF-MOEIDIED-Since:**（告诉服务器，我的缓存中有这个文件，改文件的事件是...）

​	**Referer:https://blog.csdn.net/**（告诉服务器，我来自哪里，可用于防盗链，防下载）

​	**User-Agent:Mozilla/5.0 (Windows NT 10.0; WOW64) ...**（告诉服务器浏览器内核）

​	**Cookie:CPROID=E2506C5567A4AB68687E50500B3F9AEA...jsessionid=5F4771183629C983**

​	**Cache-Control:no-cache**（对缓存进行控制，缓存一年，或不希望被缓存就可以通过这个报文头达到目的）

​	*（空行）*

​	*请求数据：*

​	**name=navicate&pwd=123456**（key=value格式）

​	

服务器是怎么知道客户端的多个请求是隶属于一个Session呢？注意到后台的那个**jsessionid = 5F4771183629C983**木有？原来就是通过HTTP请求报文头的Cookie属性的jsessionid的值关联起来的！（当然也可以通过重写URL的方式将会话ID附带在每个URL的后面）。

#### 3.2 服务器响应消息

格式：状态行，消息头，空行和响应正文

​	*状态行：*

​	**HTTP1.1 200 OK** （协议名称及版本号、状态码、简要描述）

​	*消息头：*

​	**Location:https://www.baidu.com**（重定向页面URL）

​	**Server:Apache tomcat** （告诉浏览器，服务器软件名称）

​	**Content-Encoding:gzip**（告诉浏览器，使用了gzip压缩方式）

​	**Content-Language:zh-cn**（支持的语言）

​	**Content-Length:80**（返回数据字节大小）

​	**Last-Modified:**（上次修改的时间）

​	**Refresh:1,url=https://blog.csdn.net/**（过1s刷新到对应网址）

​	**Content-Disposition;attachment;filename=aaa.zip**（告诉浏览器有文件下载）

​	**Cookie:CPROID=E2506C5567A4AB68687E50500B3F9AEA...jsessionid=5F4771183629C983**

​	**Expies:-1**（不缓存页面）

​	*（空行）*

​	*响应正文：*

​	**{“password”:"1234","username":"tom"}**



**响应状态码**

- 1xx 消息，一般是告诉客户端，请求已经收到了，正在处理，别急...
- 2xx 处理成功，一般表示：请求收悉、我明白你要的、请求已受理、已经处理完成等信息.
- 3xx 重定向到其它地方。它让客户端再发起一个请求以完成整个处理。
- 4xx 处理发生错误，责任在客户端，如客户端的请求一个不存在的资源，客户端未被授权，禁止访问等。
- 5xx 处理发生错误，责任在服务端，如服务端抛出异常，路由出错，HTTP版本不支持等。

**常见状态码**

- 200 (OK): 找到了该资源，并且一切正常。

- 302/307：临时重定向，指出请求的文档已被临时移动到别处, 此文档的新的url在location响应头中给出

- 304 (NOT MODIFIED): 该资源在上次请求之后没有任何修改。这通常用于浏览器的缓存机制。

- 401 (UNAUTHORIZED): 客户端无权访问该资源。这通常会使得浏览器要求用户输入用户名和密码，以登录到服务器。

- 403 (FORBIDDEN): 客户端未能获得授权。这通常是在401之后输入了不正确的用户名或密码。

- 404 (NOT FOUND): 在指定的位置不存在所申请的资源。
- 500 (Internal Server Error):这个错误，往往是服务端异常

#### 3.3 常见属性

- **Cache-Control**

请求中使用该字段的可选值有：

![1562567804692](picture\20180921155944100.png)

响应输出到客户端后，服务端通过该报文头属告诉客户端如何控制响应内容的缓存。

![1562569822846](picture\1562569822846.png)

*默认为private，缓存时间为31536000秒（365天）也就是说，在365天内再次请求这条数据，都会直接获取缓存数据库中的数据，直接使用。

- **ETag**

单纯的以修改时间来判断还是有缺陷，比如文件的最后修改时间变了，但内容没变。对于这样的情况，我们可以使用etag来处理。etag的方式是这样：服务器通过某个算法对资源进行计算，取得一串值(类似于文件的哈希值)，之后将该值通过etag返回给客户端，客户端下次请求时通过If-None-Match带上该值，服务器对该值进行对比校验：如果相同，就将**If-None-Match**的值设为**false**，返回状态为**304**，客户端继续使用本地缓存。如果不同就将**If-None-Match**的值设为**true**，返回状态为**200**，客户端重新解析服务器返回的数据。
- **Set-Cookie**

服务端可以设置客户端的Cookie，其原理就是通过这个响应报文头属性实现的

> Set-Cookie: UserID=JohnDoe; Max-Age=3600; Version=1    

- **Cookie机制**

客户端请求服务器，如果服务器需要记录该用户状态，就使用response向客户端浏览器颁发一个Cookie。客户端浏览器会把Cookie保存起来。当浏览器再请求该网站时，浏览器把请求的网址连同该Cookie一同提交给服务器。服务器检查该Cookie，以此来辨认用户状态。服务器还可以根据需要修改Cookie的内容。

Cookie的maxAge决定着Cookie的有效期，单位为秒（Second）。Cookie中通过getMaxAge()方法与setMaxAge(int maxAge)方法来读写maxAge属性。

如果maxAge属性为正数，则表示该Cookie会在maxAge秒之后自动失效。

如果maxAge为负数，则表示该Cookie仅在本浏览器窗口以及本窗口打开的子窗口内有效，关闭窗口后该Cookie即失效。

如果maxAge为0，则表示删除该Cookie。

Cookie并不提供修改、删除操作。如果要修改某个Cookie，只需要新建一个同名的Cookie，添加到response中覆盖原来的Cookie。

如果要删除某个Cookie，只需要新建一个同名的Cookie，并将maxAge设置为0，并添加到response中覆盖原来的Cookie即可

### 4. 请求方法

根据 HTTP 标准，HTTP 请求可以使用多种请求方法。

HTTP1.0 定义了三种请求方法： GET, POST 和 HEAD方法。

HTTP1.1 新增了六种请求方法：OPTIONS、PUT、PATCH、DELETE、TRACE 和 CONNECT 方法。

| 序  号 | 方法    | 描述                                                         |
| :----- | :------ | :----------------------------------------------------------- |
| 1      | GET     | 请求指定的页面信息，并返回实体主体。                         |
| 2      | HEAD    | 类似于 GET 请求，只不过返回的响应中没有具体的内容，用于获取报头 |
| 3      | POST    | 向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST 请求可能会导致新的资源的建立和/或已有资源的修改。 |
| 4      | PUT     | 从客户端向服务器传送的数据取代指定的文档的内容。             |
| 5      | DELETE  | 请求服务器删除指定的页面。                                   |
| 6      | CONNECT | HTTP/1.1 协议中预留给能够将连接改为管道方式的代理服务器。    |
| 7      | OPTIONS | 允许客户端查看服务器的性能。                                 |
| 8      | TRACE   | 回显服务器收到的请求，主要用于测试或诊断。                   |
| 9      | PATCH   | 是对 PUT 方法的补充，用来对已知资源进行局部更新 。           |

### 5. GET和POST的区别

我们先看一下 **w3school**标准答案长什么样子

![img](https://pics3.baidu.com/feed/1b4c510fd9f9d72ab60c3af13e6e5830359bbbbf.jpeg?token=2d144f5a8eaffc33e6a662d162698007&s=E190E1331F1C55C8465D6CDA0100E0B3)

从标准上来看，GET 和 POST 的区别如下：

GET 用于获取信息，是无副作用的，是幂等的，且可缓存

 POST 用于修改服务器上的数据，有副作用，非幂等，不可缓存

![img](https://pics7.baidu.com/feed/b3119313b07eca805fa1da517667e7d9a04483ad.jpeg?token=769f7e081452afbe924b04c409cf0c08&s=0558C1324952C0CE0AEDF4DF030010B2)

**GET、POST 请求报文上的区别**

GET 和 POST 只是 HTTP 协议中两种请求方式（异曲同工），而 HTTP 协议是基于 TCP/IP 的应用层协议，无论 GET 还是 POST，用的都是同一个传输层协议，所以在传输上，没有区别。

报文格式上，不带参数时，最大区别就是第一行方法名不同, 仅仅是报文的几个字符不同而已

POST 方法请求报文第一行是这样的 POST /url HTTP/1.1 GET 方法请求报文第一行是这样的 GET /url HTTP/1.1

带参数时报文的区别呢？ 在约定中，GET 方法的参数应该放在 url 中，POST 方法参数应该放在 body 中

举个例子，如果参数是 name=Javanx, age=25。

**GET 方法报文是这样的**

GET /updateInfo?name=Javanx&age=25 HTTP/1.1 Host: localhost（示例）下图是头条获取文章列表请求

![img](https://pics0.baidu.com/feed/8601a18b87d6277f23e8027ecc7c6f34e824fc43.jpeg?token=8689d860d962b2be325a845775789e25&s=1EAA74230F0B54495C7524D60000C0B2)

**POST 方法报文是这样的**

POST /updateInfo HTTP/1.1 Host: localhost Content-Type: application/x-www-form-urlencoded name=Javanx&age=25（示例）下图是头条回复评论请求

![img](https://pics5.baidu.com/feed/a50f4bfbfbedab646a274bda1f72dfc779311e1f.jpeg?token=bd27388eeb7db847f1e41fb94151f10f&s=BE2A74230B0B41491C7DA0D60000C0B2)

现在我们知道了两种方法本质上是 TCP 连接，没有区别。但如果不按规范来也是可以的，可以在 URL 上写参数，然后方法使用 POST；也可以在 Body 写参数，然后方法使用 GET。当然，这需要服务端支持。

**GET 方法参数写法是固定的吗？**

在约定中，我们的参数是写在 ? 后面，用 & 分割。

我们知道，解析报文的过程是通过获取 TCP 数据，用正则等工具从数据中获取 Header 和 Body，从而提取参数。

比如header请求头中添加token，来验证用户是否登录等权限问题。

也就是说，我们可以自己约定参数的写法，只要服务端能够解释出来就行，万变不离其宗。

**GET 方法的长度限制是怎么回事？**

网络上都会提到浏览器地址栏输入的参数是有限的。

首先说明一点，HTTP 协议没有 Body 和 URL 的长度限制，对 URL 限制的大多是浏览器和服务器的原因。

浏览器原因就不说了，服务器是因为处理长 URL 要消耗比较多的资源，为了性能和安全（防止恶意构造长 URL 来攻击）考虑，会给 URL 长度加限制。

**POST 方法比 GET 方法安全？**

有人说POST 比 GET 安全，因为数据在地址栏上不可见。

然而，从传输的角度来说，他们都是不安全的，因为 HTTP 在网络上是明文传输的，只要在网络节点上捉包，就能完整地获取数据报文。

要想安全传输，就只有加密，也就是 HTTPS。

**POST 方法会产生两个 TCP 数据包？**

有些文章中提到，post 会将 header 和 body 分开发送，先发送 header，服务端返回 100 状态码再发送 body。

HTTP 协议中没有明确说明 POST 会产生两个 TCP 数据包，而且实际测试(Chrome)发现，header 和 body 不会分开发送。

所以，header 和 body 分开发送是部分浏览器或框架的请求方法，不属于 post 必然行为。

### 6. 常见的WEB攻击

#### 6.1 XSS跨站脚本攻击

SQL注入与OS命令

远程文件的漏洞

会话劫持与伪请求

密码破解

### 7. 面试常见问题

#### 7.1 什么是持久连接？ 

持久连接即keep alive模式，指的是据传输完成了保持TCP连接不断开（不发RST包、不四次挥手），等待在同域名下继续用这个通道传输数据，持久连接使客户端到服务器端连接持续有效，避免了重新建立连接。这大大减少了连接的建立以及关闭时延。HTTP连接是建立在TCP协议之上的，建立一条TCP连接需要三次握手，TCP连接关闭时需要四次挥手。这些都是需要时间的。

#### 7.2 什么是管线化？

通常http请求总是顺序发送的，下一个请求只有在当前请求的响应被完全接受的时候才会被发送。由于网络延迟和带宽的限制，这样会导致在服务器发送下一个响应的时候中间有很大的延迟。HTTP/1.1允许多个http请求通过一个套接字同时被输出 ，而不用等待相应的响应。然后请求者就会等待各自的响应，这些响应是按照之前请求的顺序依次到达。（me：所有请求保持一个FIFO的队列，一个请求发送完之后，不必等待这个请求的响应被接收到，下一个请求就可以被再次发出；同时，服务器端返回这些请求的响应时也是按照FIFO的顺序）。管道化的表现可以大大提高页面加载的速度，尤其是在高延迟连接中。

## HTTPS

### 一、什么是HTTPS？

> 《图解HTTP》这本书中曾提过HTTPS是身披SSL外壳的HTTP。HTTPS是一种通过计算机网络进行安全通信的传输协议，经由HTTP进行通信，利用SSL/TLS建立全信道，加密数据包。HTTPS使用的主要目的是提供对网站服务器的身份认证，同时保护交换数据的隐私与完整性。
>
> PS:TLS是传输层加密协议，前身是SSL协议，由网景公司1995年发布，有时候两者不区分。

### 二、HTTP VS HTTPS

**HTTP特点：**

无状态：协议对客户端没有状态存储，对事物处理没有“记忆”能力，比如访问一个网站需要反复进行登录操作
无连接：HTTP/1.1之前，由于无状态特点，每次请求需要通过TCP三次握手四次挥手，和服务器重新建立连接。比如某个客户机在短时间多次请求同一个资源，服务器并不能区别是否已经响应过用户的请求，所以每次需要重新响应请求，需要耗费不必要的时间和流量。
基于请求和响应：基本的特性，由客户端发起请求，服务端响应
简单快速、灵活
通信使用明文、请求和响应不会对通信方进行确认、无法保护数据的完整性
下面通过一个简单的抓包实验观察使用HTTP请求传输的数据：

![img](https://img-blog.csdn.net/20180723103319469?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9taW5nMTAwMDAx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![img](https://img-blog.csdn.net/20180719135617449?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9taW5nMTAwMDAx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


结果分析：HTTP协议传输数据以明文形式显示

针对无状态的一些解决策略：

场景：逛电商商场用户需要使用的时间比较长，需要对用户一段时间的HTTP通信状态进行保存，比如执行一次登陆操作，在30分钟内所有的请求都不需要再次登陆。

通过Cookie/Session技术
HTTP/1.1持久连接（HTTP keep-alive）方法，只要任意一端没有明确提出断开连接，则保持TCP连接状态，在请求首部字段中的Connection: keep-alive即为表明使用了持久连接
**HTTPS特点：**

基于HTTP协议，通过SSL或TLS提供加密处理数据、验证对方身份以及数据完整性保护

![img](https://img-blog.csdn.net/20180719135629906?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9taW5nMTAwMDAx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


通过抓包可以看到数据不是明文传输，而且HTTPS有如下特点：

- 内容加密：采用混合加密技术，中间者无法直接查看明文内容
- 验证身份：通过证书认证客户端访问的是自己的服务器
- 保护数据完整性：防止传输的内容被中间人冒充或者篡改

**混合加密：**结合非对称加密和对称加密技术。客户端使用对称加密生成密钥对传输数据进行加密，然后使用非对称加密的公钥再对秘钥进行加密，所以网络上传输的数据是被秘钥加密的密文和用公钥加密后的秘密秘钥，因此即使被黑客截取，由于没有私钥，无法获取到加密明文的秘钥，便无法获取到明文数据。

**数字摘要：**通过单向hash函数对原文进行哈希，将需加密的明文“摘要”成一串固定长度(如128bit)的密文，不同的明文摘要成的密文其结果总是不相同，同样的明文其摘要必定一致，并且即使知道了摘要也不能反推出明文。

**数字签名技术：**数字签名建立在公钥加密体制基础上，是公钥加密技术的另一类应用。它把公钥加密技术和数字摘要结合起来，形成了实用的数字签名技术。

- 收方能够证实发送方的真实身份；
- 发送方事后不能否认所发送过的报文；
- 收方或非法者不能伪造、篡改报文。

![img](https://img-blog.csdn.net/20180719103559793?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9taW5nMTAwMDAx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


非对称加密过程需要用到公钥进行加密，那么公钥从何而来？其实公钥就被包含在数字证书中，数字证书通常来说是由受信任的数字证书颁发机构CA，在验证服务器身份后颁发，证书中包含了一个密钥对（公钥和私钥）和所有者识别信息。数字证书被放到服务端，具有服务器身份验证和数据传输加密功能。

### 三、HTTP通信传输

![img](https://img-blog.csdn.net/20180719094739178?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9taW5nMTAwMDAx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

客户端输入URL回车，DNS解析域名得到服务器的IP地址，服务器在80端口监听客户端请求，端口通过TCP/IP协议（可以通过Socket实现）建立连接。HTTP属于TCP/IP模型中的运用层协议，所以通信的过程其实是对应数据的入栈和出栈。

![img](https://img-blog.csdn.net/20180719094756330?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9taW5nMTAwMDAx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

报文从运用层传送到运输层，运输层通过TCP三次握手和服务器建立连接，四次挥手释放连接。

![img](https://img-blog.csdn.net/20180719110828114?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9taW5nMTAwMDAx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

为什么需要三次握手呢？为了防止已失效的连接请求报文段突然又传送到了服务端，因而产生错误。

比如：client发出的第一个连接请求报文段并没有丢失，而是在某个网络结点长时间的滞留了，以致延误到连接释放以后的某个时间才到达server。本来这是一个早已失效的报文段，但是server收到此失效的连接请求报文段后，就误认为是client再次发出的一个新的连接请求，于是就向client发出确认报文段，同意建立连接。假设不采用“三次握手”，那么只要server发出确认，新的连接就建立了，由于client并没有发出建立连接的请求，因此不会理睬server的确认，也不会向server发送数据，但server却以为新的运输连接已经建立，并一直等待client发来数据。所以没有采用“三次握手”，这种情况下server的很多资源就白白浪费掉了。

![img](https://img-blog.csdn.net/20180719110841774?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9taW5nMTAwMDAx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

为什么需要四次挥手呢？TCP是全双工模式，当client发出FIN报文段时，只是表示client已经没有数据要发送了，client告诉server，它的数据已经全部发送完毕了；但是，这个时候client还是可以接受来server的数据；当server返回ACK报文段时，表示它已经知道client没有数据发送了，但是server还是可以发送数据到client的；当server也发送了FIN报文段时，这个时候就表示server也没有数据要发送了，就会告诉client，我也没有数据要发送了，如果收到client确认报文段，之后彼此就会愉快的中断这次TCP连接。

### 四、HTTPS实现原理

![img](http://on-img.com/chart_image/5b503d10e4b0edb750e0d4f8.png)

1. client向server发送请求https://baidu.com，然后连接到server的443端口。

2. 服务端必须要有一套数字证书，可以自己制作，也可以向组织申请。区别就是自己颁发的证书需要客户端验证通过，才可以继续访问，而使用受信任的公司申请的证书则不会弹出提示页面，这套证书其实就是一对公钥和私钥。

3. 传送证书
     这个证书其实就是公钥，只是包含了很多信息，如证书的颁发机构，过期时间、服务端的公钥，第三方证书认证机构(CA)的签名，服务端的域名信息等内容。

4. 客户端解析证书
     这部分工作是由客户端的TLS来完成的，首先会验证公钥是否有效，比如颁发机构，过期时间等等，如果发现异常，则会弹出一个警告框，提示证书存在问题。如果证书没有问题，那么就生成一个随即值（秘钥）。然后用证书对该随机值进行加密。

5. 传送加密信息
     这部分传送的是用证书加密后的秘钥，目的就是让服务端得到这个秘钥，以后客户端和服务端的通信就可以通过这个随机值来进行加密解密了。

6. 服务端加密信息
     服务端用私钥解密秘密秘钥，得到了客户端传过来的私钥，然后把内容通过该值进行对称加密。

7. 传输加密后的信息
     这部分信息是服务端用私钥加密后的信息，可以在客户端被还原。

8. 客户端解密信息
     客户端用之前生成的私钥解密服务端传过来的信息，于是获取了解密后的内容。

问题：
1.怎么保证保证服务器给客户端下发的公钥是真正的公钥，而不是中间人伪造的公钥呢？

![img](https://img-blog.csdn.net/20180724090424143?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9taW5nMTAwMDAx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![img](https://img-blog.csdn.net/20180719095555854?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9taW5nMTAwMDAx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


2.证书如何安全传输，被掉包了怎么办？

- 数字证书包括了加密后服务器的公钥、权威机构的信息、服务器域名，还有经过CA私钥签名之后的证书内容（经过先通过Hash函数计算得到证书数字摘要，然后用权威机构私钥加密数字摘要得到数字签名)，签名计算方法以及证书对应的域名。当客户端收到这个证书之后，使用本地配置的权威机构的公钥对证书进行解密得到服务端的公钥和证书的数字签名，数字签名经过CA公钥解密得到证书信息摘要，然后根据证书上描述的计算证书的方法计算一下当前证书的信息摘要，与收到的信息摘要作对比，如果一样，表示证书一定是服务器下发的，没有被中间人篡改过。因为中间人虽然有权威机构的公钥，能够解析证书内容并篡改，但是篡改完成之后中间人需要将证书重新加密，但是中间人没有权威机构的私钥，无法加密，强行加密只会导致客户端无法解密，如果中间人强行乱修改证书，就会导致证书内容和证书签名不匹配。
- 那第三方攻击者能否让自己的证书显示出来的信息也是服务端呢？（伪装服务端一样的配置）显然这个是不行的，因为当第三方攻击者去CA那边寻求认证的时候CA会要求其提供例如域名的whois信息、域名管理邮箱等证明你是服务端域名的拥有者，而第三方攻击者是无法提供这些信息所以他就是无法骗CA他拥有属于服务端的域名

### 五、运用与总结

**安全性考虑：**

1. HTTPS协议的加密范围也比较有限，在黑客攻击、拒绝服务攻击、服务器劫持等方面几乎起不到什么作用
2. SSL证书的信用链体系并不安全，特别是在某些国家可以控制CA根证书的情况下，中间人攻击一样可行

> 中间人攻击（MITM攻击）是指，黑客拦截并篡改网络中的通信数据。又分为被动MITM和主动MITM，被动MITM只窃取通信数据而不修改，而主动MITM不但能窃取数据，还会篡改通信数据。最常见的中间人攻击常常发生在公共wifi或者公共路由上。

**成本考虑：**

1. SSL证书需要购买申请，功能越强大的证书费用越高
2. SSL证书通常需要绑定IP，不能在同一IP上绑定多个域名，IPv4资源不可能支撑这个消耗（SSL有扩展可以部分解决这个问题，但是比较麻烦，而且要求浏览器、操作系统支持，Windows XP就不支持这个扩展，考虑到XP的装机量，这个特性几乎没用）。
3. 根据ACM CoNEXT数据显示，使用HTTPS协议会使页面的加载时间延长近50%，增加10%到20%的耗电。
4. HTTPS连接缓存不如HTTP高效，流量成本高。
5. HTTPS连接服务器端资源占用高很多，支持访客多的网站需要投入更大的成本。
6. HTTPS协议握手阶段比较费时，对网站的响应速度有影响，影响用户体验。比较好的方式是采用分而治之，类似12306网站的主页使用HTTP协议，有关于用户信息等方面使用HTTPS。

## 参考文献

> 1. 《HTTP协议分析写的相当详细相当好》：https://blog.csdn.net/zcw4237256/article/details/80501109
> 2. 《 Etag & If-None-Match 专题]》https://www.cnblogs.com/softidea/p/5986339.html
> 3. 《浅谈http中的Cache-Control》https://blog.csdn.net/u012375924/article/details/82806617
> 4. 《HTTP/1.1管道化问答》 https://jiaolonghuang.github.io/2015/08/16/http-pipelining/
> 5. 《HTTP 请求方法》：https://www.runoob.com/http/http-methods.html
> 6. 《面试必备：GET和POST的区别详细解说》：https://baijiahao.baidu.com/s?id=1626599028653203490
> 7. 《HTTP和HTTPS协议，看一篇就够了》：https://blog.csdn.net/xiaoming100001/article/details/81109617

