# Servlet生命周期

![img](https://uploadfiles.nowcoder.com/images/20170823/3252049_1503478423363_D9B23E5A78E8071F0BB0D7E0E456058E)

# ServletContext

* 使用ServletContext注册web组件(Servlet,Filter,Listener)
* 使用编码方式，在项目启动的时候给ServletContext里面添加组件
  * ServletContainerInitializer得到的ServletContext
  * ServletContextListener提到的ServletContext



# Session机制

HttpServletResponse 接口提供了重写 URL 的方法：

```java
public java.lang.String encodeURL(java.lang.String url) 
```

   该方法的实现机制为： 

​    ● 先判断当前的 Web 组件是否启用 Session，如果没有启用 Session，直接返回参数 url。 

​    ● 再判断客户端浏览器是否支持 Cookie，如果支持 Cookie，直接返回参数 url；如果不支持 Cookie，就在参数 url 中加入 Session ID 信息，然后返回修改后的 url。

# [ Servlet如何处理多个请求访问](https://www.jianshu.com/p/abb2221fc6e7)

* Servlet容器默认是采用单实例多线程的方式处理多个请求的：
  * 1.当web服务器启动的时候（或客户端发送请求到服务器时），Servlet就被加载并实例化(只存在一个Servlet实例)；
  * 2.容器初始化化Servlet主要就是读取配置文件（例如tomcat,可以通过servlet.xml的设置线程池中线程数目，初始化线程池通过web.xml,初始化每个参数值等等。
  * 3.当请求到达时，Servlet容器通过调度线程(Dispatchaer Thread) 调度它管理下线程池中等待执行的线程（Worker Thread）给请求者；
  * 4.线程执行Servlet的service方法；
  * 5.请求结束，放回线程池，等待被调用；

  （注意：避免使用实例变量（成员变量），因为如果存在成员变量，可能发生多线程同时访问该资源时，都来操作它，照成数据的不一致，因此产生线程安全问题）

# [如何控制Servlet的线程安全性](https://www.jianshu.com/p/82a2a4fcd0c9)

## 变量的线程安全

* 我们知道当没有实例变量的时候，就基本不存在线程不安全的问题了，所以**不使用实例变量是一种方法**。

## 属性的线程安全

- ServletContext，不是线程安全的，多线程可以同时读写。使用时要注意。
- HttpSession，不是线程安全的，比如用户打开多个浏览器窗口时候就会产生多个请求针对同一个session的操作。使用时要注意。
- ServletRequest，线程安全的，它对应着一个request请求，所以说是线程安全的。

# [DispatcherServlet的线程安全性](https://www.jianshu.com/p/82a2a4fcd0c9)

* 在应用启动的时候，就会根据web.xml中配置的有关Spring和SpringMVC的配置启动初始化，**对于SpringMVC初始化的是DispatcherServlet**，对于Servlet初始化只会进行一次，并且只有一个实例，所以DispatcherServlet只会存在一个。

* 但是当多线程同时访问DispatcherServlet的时候是线程安全的，因为**DispatcherServlet中的内部属性都不会影响线程安全，所以DispatcherServlet可以忽略线程安全的问题**。

* 虽然DispatcherServlet可以认为是线程安全的，但是SpringMVC中的Controller不是。Controller也是单例的，每个请求对应一个Controller中的方法，方法如果没有使用实例变量，可以认为是线程安全的，但是如果有实例变量就要考虑线程安全的问题了













