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

# 在浏览器地址栏键入URL,按下回车后经历的流程

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

# HTTP状态码

* 1xx	

  * 指示信息--表示请求已接收,继续处理
* 2xx

  * 成功--表示请求已被成功接收,理解,接收
  * 200 OK--正常的返回信息
* 3xx

  * 重定向--要完成请求必须进行更进一步的操作
* 4xx
  * 客户端错误--请求有语法错误或请求无法实现
  * 400 BadRequest--客户端请求有语法错误,不能被服务器所理解
  * 401 Unauthorized--请求未经授权,这个状态代码必须和WWW-Authenticate报头一起使用
  * 403 Forbidden--服务器收到请求,但是拒绝提供服务
  * 404 Not Found--请求资源不存在
* 5xx
  * 服务端错误--服务器未能实现合法的请求
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

# Cookie和Session的区别

* Sessioon的实现方式
  * 使用Cookie来实现(JSESSIONID)
  * 使用URL回写来实现
* Cookie数据存放在客户的浏览器上,Session数据放在服务器上
* Session相对Cookie更安全
* 若考虑减轻服务器负担,应当使用Cookie





