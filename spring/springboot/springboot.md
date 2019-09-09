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
* @Configuration  
  * 表示这是一个配置类,可给容器加组件
* @EnableConfigurationProperties(xx.class)
  * 启用指定类(xx.class)ConfigurationProperties功能
* @ConditionalOnClass(xxx.class)
  * 判断当前项目是否有xxx类
* @ConditionalOnProperty

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
* @Conditional
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

    `spring.profile.active = dev`
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
* 静态资源加载顺序
  * META/resources 
  *  resources 
  *  static 
  *  public

# 自动配置原理

* SpringBoot启动的时候加载主配置类，开启了自动配置功能@EnableAutoConfiguration

* @EnableAutoConfiguration作用

  * 利用EnableAutoConfigurationImportSelector给容器导入一些组件

  ```java
  List<String> configurations = SpringFactoriesLoader.loadFactoryNames(
  				getSpringFactoriesLoaderFactoryClass(), getBeanClassLoader());
  //扫描所有jar包类路径下 "META-INF/spring.factories"
  //把扫描到的这些文件的内容包装成properties
  //从properties中获取到EnableAutoCpnfigutation.class类(类名)对应的值,然后把他们添加在容器中
  ```

  * 即将类路径下META-INF/spring.factories里面配置的所有EnableAutoConfiguration的值加入到了容器中

    ```properties
    org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
    org.springframework.cloud.netflix.archaius.ArchaiusAutoConfiguration,\
    org.springframework.cloud.netflix.feign.ribbon.FeignRibbonClientAutoConfiguration,\
    org.springframework.cloud.netflix.feign.FeignAutoConfiguration,\
    org.springframework.cloud.netflix.feign.encoding.FeignAcceptGzipEncodingAutoConfiguration,\
    org.springframework.cloud.netflix.feign.encoding.FeignContentGzipEncodingAutoConfiguration,\
    org.springframework.cloud.netflix.hystrix.HystrixAutoConfiguration,\
    org.springframework.cloud.netflix.hystrix.security.HystrixSecurityAutoConfiguration,\
    org.springframework.cloud.netflix.ribbon.RibbonAutoConfiguration,\
    org.springframework.cloud.netflix.rx.RxJavaAutoConfiguration,\
    org.springframework.cloud.netflix.metrics.servo.ServoMetricsAutoConfiguration
    
    org.springframework.cloud.client.circuitbreaker.EnableCircuitBreaker=\
    org.springframework.cloud.netflix.hystrix.HystrixCircuitBreakerConfiguration
    
    org.springframework.boot.env.EnvironmentPostProcessor=\
    org.springframework.cloud.netflix.metrics.ServoEnvironmentPostProcessor
    ```

    * 每一个这样的xxAutoConfiguration类都是容器中的一个组件,都加入到容器中,用他们来做自动配置

  * 每一个自动配置类做自动配置功能

    * 根据当前不同的条件判断,决定这个配置类是否生效

* 精髓

  * SpringBoot启动会加载大量的自动配置类

  * 我们看我们需要的功能有没有SpringBoot默认写好的自动配置类

  * 我们再来看这个自动配置类到底配置了哪些组件

    * 如果有我们要用的组件，就不需要再来配置了

  * 给容器中自动配置类添加组件的时候，会从properties类中获取某些属性，我们就可以在配置文件的中指定这些属性的值

* 自动配置类必须在一定条件下才能生效
  * 通过启用debug=true;让控制台打印自动配置报告
  * Positive matches(已启用)
  * Negative matches(未启用)

#     日志

* 日志门面(抽象类)

  * SLF4j	

* 日志实现

  * logback

  * log4j

## 如何让系统中所有的日志都统一到slf4j

* 将系统中其他日志框架先排除出去
* 用中间包替换原有的日志框架
* 导入slf4j其他实现

## 日志的级别

* trace
* debug
* info
* warn
* error

```properties
logging.level.com.soto= trace
logging.path=
logging.file=
logging.patterm.console=
logging.pattern.file=
```

## 指定配置

* logback.xml

  * 直接被日志框架识别

* logback-spring.xml

  * 由spring 解析日志配置,可以使用Spring Boot的Profile功能

  ```properties
  <springProfile name="staging">
  </springProfile>
  ```

# web开发

模板引擎实时生效

* 禁用模板引擎的缓存
* 页面修改完成以后，ctrl+F9重新编译

拦截器

* LoginHandlerInterceptor implements HandlerInterceptor

  ```java
  @Override
      public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
          Object user = request.getSession().getAttribute("loginUser");
          return false;
      }
  
      @Override
      public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
  
      }
  
      @Override
      public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
  
      }
  ```

thymeleaf公共页面抽取

* th:insert
  * 将公共片段整个插入到声明引入元素中
* th:replace
  * 将声明的引入的元素替换为公共片段
* th:include
  * 将被引入的片段的内容包含进标签中



# 启动配置原理

* 几个重要的事件回调机制
  * 配置在META-INF/spring.factories
    * ApplicationContextInitializer
    * SpringApplicationRunListener
  * 只需要放在IOC容器中
    * ApplicationRunner
    * CommandLineRunner

* 创建SpringApplication对象

  ```java
  private void initialize(Object[] sources) {
      //保存主配置类
  		if (sources != null && sources.length > 0) {
  			this.sources.addAll(Arrays.asList(sources));
  		}
      //判断当前是否是一个web应用
  		this.webEnvironment = deduceWebEnvironment();
      //从类路径下找到META-INF/spring.factories配置的所有ApplicationContextInitializer,然后保存起来
  		setInitializers((Collection) getSpringFactoriesInstances(
  				ApplicationContextInitializer.class));
      //从类路径下找到META-INF/spring.factories配置的所有ApplicationListener
  		setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
      
      //从多个配置类中找到有main方法的主配置类
  		this.mainApplicationClass = deduceMainApplicationClass();
  	}
  ```

  ```java
  public ConfigurableApplicationContext run(String... args) {
  		StopWatch stopWatch = new StopWatch();
  		stopWatch.start();
  		ConfigurableApplicationContext context = null;
  		FailureAnalyzers analyzers = null;
  		configureHeadlessProperty();
      
      //获取SpringApplicationRunListeners;从类路径下META-INF/spring.factories获取
  		SpringApplicationRunListeners listeners = getRunListeners(args);
      
      //回调所有的获取SpringApplicationRunListeners.starting()方法
  		listeners.starting();
      
  		try {
  			ApplicationArguments applicationArguments = new DefaultApplicationArguments(
  					args);
              //准备环境,创建环境完成后回调SpringApplicationRunListeners.environmentPrepared();表示环境准备完成
  			ConfigurableEnvironment environment = prepareEnvironment(listeners,
  					applicationArguments);
  			Banner printedBanner = printBanner(environment);
              
              //创建ApplicationContext决定创建web的ioc还是普通的IOC
  			context = createApplicationContext();
  			analyzers = new FailureAnalyzers(context);
              
              //准备上下文环境,将environment保存到ioc中,
              //applyInitializers()回调之前保存的ApplicationContextInitializer的initialize()方法
              //回调所有的SpringApplicationRunListener的contextPrepared()
  			prepareContext(context, environment, listeners, applicationArguments,
  					printedBanner);
              
              //刷新容器;ioc容器初始化
              //扫描创建加载所有组件的地方
  			refreshContext(context);
              
              //从所有的ioc容器中获取所有的ApplicationRunner和CommandLineRunner进行回调
  			afterRefresh(context, applicationArguments);
              //所有的SpringApplicationRunListener回调finished
  			listeners.finished(context, null);
              
  			stopWatch.stop();
  			if (this.logStartupInfo) {
  				new StartupInfoLogger(this.mainApplicationClass)
  						.logStarted(getApplicationLog(), stopWatch);
  			}
              
              //整个SpringBoot应用启动完成后返回IOC容器
  			return context;
  		}
  		catch (Throwable ex) {
  			handleRunFailure(context, listeners, analyzers, ex);
  			throw new IllegalStateException(ex);
  		}
  	}
  ```

# 自定义starter

* starter

  * 这个场景需要使用到的依赖

  * 如何编写自动配置

    ```java
    @configuration //指定这个类是一个配置类
    @ConditionalOnxxx //在指定条件成立的情况下自动配置类生效
    @AutoConfigureOrder  //指定自动配置类的顺序
    @Bean  //给容器中添加组件
    @ConfigurationProperties //结合相关xxxProperties类来绑定相关配置
    @EnableConfigurationProperties //让xxxProperties类生效,加入到容器中
    ```

    * 自动配置类要能加载,将需要启动就加载的自动配置类,配置在`META-INF/spring.factories`

    ```properties
    org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
    org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
    ```

  * 模式

    * 启动器只用来做依赖导入
    * 专门来写一个自动配置模块
    * 启动器依赖自动配置,别人只需要引入启动器(starter)



# Docker

* Docker镜像(Images)
  * 软件打包好的镜像,放在Docker仓库中
* Docker容器
  * 独立运行的一个或一组应用
* Docker客户端(Client)
  * 连接Docker主机进行操作
* Docker主机(Host)
  * 安装了Doscker程序的机器
* Docker仓库(Registry)
  * 用来保存各种打包好的镜像

## 常用操作

* 镜像操作
  * 搜索镜像

    > docker search mysql

  * 下载镜像

    > docker pull mysql

  * 所有镜像

    > docker images

  * 删除镜像

    > docker rmi image-id

* 容器操作

  * 运行

    > docker run --name myTomcat -d tomcat:latest 

    ```bash
    docker run -p 3306:3306 --name mysql01 -e MYSQL_ROOT_PASSWORD=123456 -d mysql
    ```

    

  * 查看运行中的容器

    >docker ps

  * 停止容器

    > docker stop containerId

  * 启动

    > docker start cocntainerId

  * 删除 

    > docker rm 

  * 端口映射

    > docker run -d -p 8080(host port):8080(container port) tomcat 

  * 容器日志

    > docker logs container -name/containerId

# RabbitMQ

* Message
* Publisher
* Exchange
* Queue
* Binding
* Connection
* Channel
* Consumer
* Virtual Host
* Broker

# Spring Boot与数据访问

* DataSourceInitializer:ApplicationListener

  * runSchemaScripts()

    * 运行建表语句

  * runDataScripts()

    * 运行插入数据sql语句

  * 默认需要将文件命名为

    ```properties
    schema-*.sql  data-*.sql
    默认规则: schema.sql  schema-all..sql;
    可以使用 
    	schema:
    		- classpath:department.sql
    		指定位置
    ```




# ElasticSearch

# 任务

## 异步任务

* @EnableAsync
* @Async

## 定时任务

* @EnableScheduling
* @Scheduled(cron=“0 * * * * MON-SAT”)

## 邮件任务

# 安全

* SpringSecurity
  * 引入SpringSecurity
  * 编写SpringSecurity的配置类
    * @EnableWebSecurity extends WebSecurityConfigurerAdapter



# 分布式

