# SpringMVC的原理

​    SpringMVC是Spring中的模块，它实现了mvc设计模式的web框架

* 用户发出请求，请求到达SpringMVC的前端控制器（DispatcherServlet）
* 前端控制器根据用户的url请求处理器映射器(HandlerMapping)查找匹配该url的handler，并返回一个执行链
* 前端控制器再请求处理器适配器（HandlerAdapter）调用相应的handler进行处理并返回给前端控制器一个modelAndView
* 前端控制器再请求视图解析器对返回的逻辑视图进行解析
* 最后前端控制器将返回的视图进行渲染并把数据装入到request域，返回给用户。

![img](https://uploadfiles.nowcoder.com/images/20171010/163192_1507646670364_7216367DD4FDC0CC274F999B0D00CFE5)