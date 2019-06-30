# 注解

* @SpringBootApplication
  * 标注一个主程序类，说明这是一个Spring Boot应用
* @SpringBootConfiguration
  * Spring Boot的配置类，标注在某个类个，表示这是一个Spring Boot的配置类
  * @Configuration
    * 配置类上来标注这个注解
* @EnableAutoConfiguration
  * 开启自动配置功能
  * Spring Boot在启动的时候从类路径下的META-INF/spring-factories中获取EnableAutoConfiguration指定的值，将这些值作为和自动配置类导入到容器中，自动配置类就生效，帮我们进行自动配置工作

