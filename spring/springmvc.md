# 什么是Spring MVC

* 基于MVC架构的用来简化web应用程序开发的框架。是Spring的一个模块
* 和Struts2一样属于表现层的框架，通过把Model，View，Controller分离，把较为复杂的web应用分成逻辑清晰的几部分，方便开发人员配合

# [Spring MVC的原理](https://mp.weixin.qq.com/s/wcK2qsZxKDJTLIGqEIyaNg)

* 用户发出请求，请求到达前端控制器（DispatcherServlet）
* 前端控制器根据用户的url请求处理器映射器(HandlerMapping)查找匹配该url的handler，并返回一个执行链
* 前端控制器再请求处理器适配器（HandlerAdapter）调用相应的handler进行处理并返回给前端控制器一个modelAndView
* 前端控制器再请求视图解析器（ViewReslover）对返回的逻辑视图进行解析
* 最后前端控制器将返回的视图进行渲染并把数据装入到request域，返回给用户。

![img](https://uploadfiles.nowcoder.com/images/20171010/163192_1507646670364_7216367DD4FDC0CC274F999B0D00CFE5)

# SpringMVC加载流程

* Servlet加载（监听器之后执行）Servlet的init()
* 加载配置文件
* 从ServletContext拿到Spring初始化springmvc相关对象
* 加入servletContext