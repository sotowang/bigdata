# 性能优化

* @SpringBootApplication注解
  * 会自动获取配置信息
  * 触发自动配置(auto-configuration)和组件扫描(component scanning)
  * 与使用@configuration,@EnableAutoConfiguration和@ComponentScan三个注解的作用是一样的
* [组件自动扫描问题](http://blog.oneapm.com/apm-tech/385.html)
  * 会导致项目启动时间变长。当启动一个大的应用程序,或将做大量的集成测试启动应用程序时，影响会特别明显。
  * 会加载一些不需要的多余的实例（beans）
  * 会增加 CPU 消耗。
* 解决方案
  * 移除 @SpringBootApplication 和 @ComponentScan 两个注解来禁用组件自动扫描
  * 在需要的 bean 上进行显式配置