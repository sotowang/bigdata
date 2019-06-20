# jsp的9大内置对象

1、request对象

request对象表示了从客端发过来的请求，主要用于接收客户端穿过来的数据，其作用域为一次请求

2、response对象

response对象表示服务端向客户端返回的响应，主要将jsp处理过的对象传回到客户端，response对象的作用域只在jsp页面有效；

3、session对象

Session对象是用来分别保存每一个用户信息的对象，以便于跟踪用户的操作状态．它保存在服务端，Session的ID保存在客户机的Cookie中．

4、application

Application可以将数据保存到服务器中，直到服务器关闭，否则Application中保存的数据在整个应用中都有效，application的声明周期相较于session、request、response较长。

application对象在服务器启动时就创建了，Application是一个单例的，多个用户共享这一个对象。

5、out对象

动态的向JSP页面输出字符流，从而把动态的内容转化成HTML形式来展示，这个对象在任何JSP页面中都可以任意访问。

6、page对象

page对象表示jsp本身，作用范围在jsp页面才是合法的

7、config对象

config对象代表当前JSP配置信息,实质上是ServletConfig的一个实例,常用来获取Servlet的初始化参数。

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

# get 与 post的区别?

* GET请求的数据会附在URL之后，以?分割URL和传输数据，参数之间以&相连，POST把提交的数据则放置在是HTTP包的包体中。
* GET的长度受限于url的长度，而url的长度限制是特定的浏览器和服务器设置的，理论上GET的长度可以无限长。
* POST是没有大小限制的，HTTP协议规范也没有进行大小限制，起限制作用的是服务器的处理程序的处理能力
* 在ASP中，服务端获取GET请求参数用Request.QueryString，获取POST请求参数用Request.Form。
* POST的安全性要比GET的安全性高

# Jsp有哪些动作？作用是什么？

* jsp:include
  * 在页面被请求的时候引入一个文件
* jsp:useBean
  * 寻找或者实例化一个JavaBean
* jsp:setProperty
  * 设置JavaBean的属性
* jsp:getProperty
  * 输出某个JavaBean的属性
* jsp:forward
  * 殷请求转到一个新的页面
* jsp:plugin
  * 根据浏览器类型为Java插件生成OBJECT或EMBED标记

 

 

 

 



