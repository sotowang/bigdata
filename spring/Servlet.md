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

 















