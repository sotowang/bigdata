# jsp的9大内置对象面试题

1、request对象

request对象表示了从客端发过来的请求，主要用于接收客户端穿过来的数据，其作用域为一次请求

2、response对象

response对象表示服务端向客户端返回的响应，主要将jsp处理过的对象传回到客户端，response对象的作用域只在jsp页面有效；

3、session对象

session对象是由服务器自动创建的与用户请求相关的对象。服务器为每一个用户生成一个session对象，用户保存该用户的信息，跟踪用户的操作状态，session对象的内部是使用Map来保存数据的，因此保存数据的格式为KEY/value，

一次会话：客户端打开浏览器连接服务器开始，到客户端浏览器关闭这个服务器结束，被称为一次会话

4、application

Application可以将数据保存到服务器中，直到服务器关闭，否则Application中保存的数据在整个应用中都有效，application的声明周期相较于session、request、response较长。

application对象在服务器启动时就创建了，Application是一个单例的，多个用户共享这一个对象。

5、out对象

Out对象用于向服务器输出信息，并且管理应用服务器上的输出缓冲区，对Out对象进行输出数据时，可以对数据缓冲区进行操作，即使清除缓冲区中的参与数据，渭南其他数据提供缓冲的空间，输出后药剂师关闭缓冲区

6、page对象

page对象表示jsp本身，作用范围在jsp页面才是合法的

7、config对象

config对象主要是去的一些服务器的配置信息，通过pageContext对象的getServletConfig()方法可以获取config对象，当服务器启动时某些信息通过config对象传递给这个servlet，开发者可以在web.xml中为应用程序环境中的servlet程序和jsp页面提供初始化参数。

8、exception

Exception对象的作用是显示异常信息，只有使用了<%@page isErrorPage="true"%>的页面才可以使用，在一般的jsp页面中使用该对象将无法编译文件

9、pageContext

pageContext页面上下文对象，可以获取任何范围的参数，对象的创建和初始化都是由容器来完成的，在jsp页面中直接使用pageContext对象



# **Forword(请求转发)与Redirect(重定向)**	

1、从数据共享上

​      Forword是一个请求的延续，可以共享request的数据

​      Redirect开启一个新的请求，不可以共享request的数据

2、从地址栏

​      Forword转发地址栏不发生变化

​      Redirect转发地址栏发生变化

# 报错的状态码

 301 永久重定向

 302 临时重定向

 304 服务端 未改变

 403 访问无权限

 200 正常

 404 路径

 500 内部错误







