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
* @Validated
  * @Email
* @ConfigurationProperties

  * 本类中所有属性和配置文件中相关配置绑定
  * 默认从全局配置文件获取值
  * prefix = "person".配置文件中哪个下面所有属性进行映射
* @Value("")

## @ConfigurationProperties  与@Value比较

|                | @ConfigurationProperties | @Value       |
| -------------- | ------------------------ | ------------ |
| 功能           | 批量注入配置文件中的属性 | 一个一个指定 |
| 松散绑定       | 支持                     | 不支持       |
| SpEL           | 不支持                   | 支持         |
| JSR303数据校验 | 支持                     | 不支持       |
| 复杂类型封装   | 支持                     | 不支持       |

* @PropertySource（value={"classpath:person.properties"}）
  * 加载指定文件
* @ImportResource
  * 导入Spring的配置文件，让配置文件中的内容生效
  * 主配置文件下注解
* @Conditiional
  * @ConditionalOnWebApplication
    * Spring底层@Conditional注解
    * 判断当前是否是web应用，如果是配置类生效
  * @ConditionalOnClass
    * 判断当前项目有没有这个类
  * @ConditionalOnPerperty
    * 判断配置文件中是否存在某个配置
  * @ConditionalOnMissingBean(xxx.class)
    * 判断容器中没有这个组件
  * @ConditionalOnBean
    * 判断是否有这个组件

# 配置文件

* application.properties

* application.yml

* application.yml

  ```yaml
  server:
  	port: 8080
  ```

* 配置文件点位符

  * 随机数
  * 点位符
    * 获取之前配置的值，如果没能可以用：指定默认值

## Profile

* 多Profile文件
  * application-dev.properties
  * applicatiion-prod.properties
  * 默认使用application.properties
  * 激活
    * **spring.profile.active = dev**
* yml的多文档块模式
* 命令行参数
  * arguments
    * --spring.profile.active=dev
* 虚拟机参数
  * VM options
    * -Dspring.profile.active=dev

## 配置文件加载位置

* 优先级(由高到低，高覆盖低，互补配置)
  * file:./config/
  * file:./
  * classpath:/config/
  * classpath:/
  * 打包后使用
    * spring.config.location

# 自动配置原理

* SpringBoot启动的时候加载主配置类，开启了自动配置功能@EnableAutoConfiguration
* @EnableAutoConfiguration作用

  * 利用AutoConfigurationImportSelector给容器导入一些组件
* 精要

  * SpringBoot启动会加载大量的自动配置类

  * 我们看我们需要的功能有没有SpringBoot默认写好的自动配置类

  * 我们再来看这个自动配置害到底配置了哪些组件

    * 如果有我们要用的组件，就不需要再来配置了

  * 给容器中自动配置类为珈组件的时候，会从properties类中获取某些属性，我们就可以在配置文件的中指定这些属性的值
* 自动配置类必须在一定条件下才能生效
  * 通过启用debug=true;让控制台打印自动配置报告
  * Positive matches(已启用)
  * Nagative matches(未启用)

#     日志

* 日志门面(抽象类)

  * SLF4j	

* 日志实现

* * logback
  * log4j

  