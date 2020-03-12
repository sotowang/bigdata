# Spring框架及其模块

* 什么是Spring框架
  * 轻量级（相对于EJB）DI和AOP框架
    * DI
      * 传统开发中，由调用者来创建被调用者实例
      * 使用IOC容器被调用对象由Spring完成，在容器实例化对象的时候主动将被调用者注入给调用对象
    * AOP
      * 将运行过程分解成切面，提取业务处理过程的切面
  * Spring不用new创建对象，而使用依赖注入，基于Spring开发的应用一般不依赖于Spring类
* 主要模块
  * IOC容器
  * 面向切面编程AOP
  * 数据访问/集成
  * Web
  * 消息模块
  * 测试模块
* Spring框架好处
  * 与EJB容器比较，IOC容器更加轻量级（2MB），IOC容器在有限的内存和CPU资源情况下进行应用程序的开发和发布变得有利

# IOC

## 依赖注入方式

* Setter
* Interface
* Constructor
* Annotation

## [Spring IOC容器创建过程](https://blog.csdn.net/hxpjava1/article/details/78307150)

* Spring容器在启动的时候，先会保存所有注册进来的Bean的定义信息
  * xml注册Bean: <bean>
  * 注解注册Bean:@Service,@Component,@Bean...
* Spring容器会在合适时机同创建这些Bean
  * 用到这个Bean的时候；利用getBean()创建Bean；创建好以合保存在容器中
  * 统一创建剩下所有bean的时候；finishBeanFactoryInitialization()
* 后置处理器
  * 每一个bean创建完成，都会使用各种后置处理器进行处理；来增强Bean功能
    * AutowiredAnnotationBeanPostProcessor：处理自动注入
    * AnnotationAwareAspectJAutoProxyCreator: 来做AOP功能（为Bean创建代理对象）
* 事件驱动模型
  * ApplicationListener：事件监听
  * ApplicationEventMulticaster: 事件派发



## Spring IOC容器的核心接口

* BeanFactory  （Spring框架最核心的接口）
  * 提供IOC的配置机制
  * 包含Bean的各种定义，便于实例化Bean
  * 建立Bean之间的依赖关系
  * Bean生命周期控制
* ApplicationContext（继承多个接口）
  * BeanFactory：能够管理，装配Bean
  * ResourcePatternResolve：能够加载资源文件
  * MessageSource：能够实现国际化等工能
  * ApplicationEventPublisher：能够注册监听器，实现监听机制

## BeanFactory与ApplicationContext区别

* BeanFactory没有扩展Spring中MessageResource接口，因此不支持国际化功能；ApplicationContext支持
* ApplicationContext事件机制通过ApplicationEvent和ApplicationListener两个接口提供，当ApplicationContext中发布事件时，所有扩展了ApplicationListener的Bean都会收到并处理（事件就涉及到观察者模式）
* ApplicationContext扩展了ResourceLoader（资源加载器接口），可用来加载多个Resource，而BeanFactory没有
* ApplicationContext在容器启动时一次性加载所有Bean，可了现Spring在配置中的错误。而BeanFactory延迟加载

## Spring IOC 支持的功能

* **依赖注入**
* 依赖检查
* **自动装配**
* 支持集合
* 指定初始化方法和销毁方法
* 支持回调方法

## getBean方法的代码逻辑

* 转换beanName
* 从缓存中加载实例
* 实例化Bean
* 检测parentBeanFactory
* 初始化依赖的Bean
* 创建Bean

![img](https://img-blog.csdn.net/20160317082518227)

## Bean的作用域(scope控制)

* singleton(默认)

  * 容器里拥有唯一的Bean实例
  * IOC容器启动会调用方法创建对象放到ico容器中
  * 以后每次获取直接从窗口(map.get())创建
  * 可使用@Lazy懒加载
* prototype
  * 针对每个getBean请求,容器都会创建一个Bean实例
  * IOC容器启动并不会去调用方法创建对象放在容器中
  * 每次获取的时候才会调用方法创建对象
* request
  * 会为每个Http请求创建一个Bean实例
* session
  * 会为每个session创建一个Bean实例
* globalSession
  * 会为每个全局Http Session 创建一个Bean实例,该作用域仅对Porlet有效

## 注解

* @Configuration
  * 配置类
* @Conditional(linux.class)
  * linux类需实现Condition接口
  * 根据条件判断给容器中注册Bean组件
* @PropertiesSources（value=""）
* @Value
  * 基本数值
    * @Value("张三")
  * #{}
    * #{20-2}
  * ${person.name}
    *  取出配置文件(在运行环境变量)中的值(properties文件)

---

### @Constructor @Autodwired @PostConstruct的顺序

* @Autowire(required=false)
  * **若类A中有个成员变量p被@Autowired,那么p是在A构造方法执行完之后注入**
  * 标注位置
    * 方法位置
      * @Bean+方法参数，参数从容器中获取
    * 构造器上
      * 如果组件只有一个有参构造器，这个有参构造器的@Autowired可以省略，参数将从容器中获取
    * 放在参数位置
  * 默认按照类型从容器中找相应组件
    * applicationContext.getBean(xxx.class)
  * 如果找到多个相同类型组件，再将属性的名称作为组件的id去容器中查找
  * @Qualifier("bookDao")
    * 指定需要装配的组件id，而非属性名
  * @Primary
    * 让Spring装配时，默认选取首选的类
    * 可以使用@Qualifier指定需要装配的bean的名字
* @PostConstruct
  * 若想在生成对象时完成初始化操作,而初始化操作又依赖于依赖注入,可使用@PostConstruct注解一个方法来完成初始化
  * 会在依赖注入完成后自动调用

* @Constructor
  * @Constructor>>@Autowired >>@PostConstruct

---



* @Resource
  * Java规范的注解，类似Autowired
  * 默认按照组件名称进行装配
    * @Resource（“指定bean name”）

* @Inject
  * Java规范的注解，类似Autowired
  * 导入javax.inject包

* @Profile("s")
  * Spring为我们提供的可以根据当前环境，动态地激活和切换一系列组件的功能
  * 加了环境标识的bean，只有这个环境被激活的时候才能注册到容器中。默认是default环境
  * 使用命令行动态参数
    * 在虚拟机参数位置加载  -Dspring.profiles.active=test
    * 代码方式激活环境
    * 没有标注环境标识的Bean在任何环境下都加载

* @EventListener(classes={ApplicationEvent.class})
  * 监听事件

  

## 给容器中注册组件（添加Bean）的方式

  * @Controller/@Service/@Repository/@Component
    * 包扫描+组件标注注解
  * @Bean
    * 导入的的第三方包里面的组件
  * @Import
    * @Import
      * 快速给容器中导入一个组件
    * 实现ImportSelector接口
      * 返回需要导入组件的全类名的数组
    * 实现ImportBeanDefinitionRefistrar
      * BeanDefinitionRegistry的registerBeanDefinition方法手工注册
  * 实现FactoryBean(工厂Bean)接口
    * 默认获取到的是工厂Bean调用getObject创建的对象
    * 要获取工厂Bean本身，我们需要给id前面加一个&标识

## Bean生命周期

* 创建
  * 单实例，在容器启动的时候创建对象
  * 多实例，每次获取的时候创建对象
* 初始化
  * 若Bean实现了BeanNameAware接口，会调用它实现的`setBeanName(String)`，传递的值为Spring配置文件中Bean的id
  * 若Bean实现了BeanFactoryAware接口，会调用它实现的`setBeanFactory(BeanFactory)`传递的是Spring工厂自身
  * 若Bean实现了ApplicationContextAware接口，会调用`setApplicationContext(ApplicationContext)`，传入Spring上下文
  * 若Bean郑不了BeanPostProcessor接口，将调用`postProcessBeforeInitialization(Object obj,String s)`,该接口经常被用于Bean内容更改，是在Bean初始化结束时调用的方法，也可应用于内存或缓存技术
  * 若Bean在Spring配置文件中配置init-method属性会自动调用其配置的初始化方法
  * 若Bean关联BeanPostProcessor接口，将会调用`postProcessAfterInitialization(Object obj,String s)`
* 销毁
  * 如果Bean实现DisposableBean接口，会调用`destroy()`方法
  * 若Bean的Spring中配置destroy-method属性，会自动调用其配置的销毁方法
* 自定义组件实现xxxAware
  * 在创建对象的时候，会调用接口的方法注入相关组件：Aware
  * 把Spring底层一些组件注入到自定义的Bean中
  * xxxAware：功能使用xxxProcessor

---

#  AOP

## 概念

在程序运行期间动态的将某段代码切入到指定方法指定位置进行运行的编程方式

- @Aspect
  - 切面类标识
- Target
  - 被织入Aspect的对象
- @EnableAspectJAutoProxy
  - 启用自动代理
- JoinPoint
  - 通过该参数获取方法信息
  - 需放在参数表的第一位
- @PointCut(execution（“”）)
  - Aspect实际被应用在的Join Point,支持正则
- Advice(通知方法)
  - 前置通知(@Before)
    - 在目标方法之前切入
  - 后置通知(@After)
  - 返回通知(@AfterReturning)
  - 异常通知(@AfterThrowing)
  - 环绕通知(@Around)
    - 动态代理，手动推进目标方法运行
    - joinPoint.procced()
- Weaving
  - AOP的实现过程

## AOP的三种织入方式

*  编译时织入
     * 需要特殊的Java编译器,如AspectJ
*  类加载时织入
   *  需要特殊的Java编译器,如AspectJ和AspectWerkz
*  运行时织入
   * Spring采用的方式,通过动态代理的方式,实现简单

## AOP原理

* 基于动态代理
  * 调用者Bean尝试调用目标方法，但是被生成代理截胡
  * 代理根据Advice种类对Advice进行调用代理调用目标方法
  * 返回调用结果给调用者Bean

### 动态代理

* 动态代理机制两个重要的类或接口
  * InvocationHandler
    * 每个动态代理都实与该接口，当通过代理对象调用一方法时，会被转发为InvocationHandler接口的`invoke`方法调用
  * Proxy
    * 用来动态创建一个代理对象的类，类中用最多的是`newProxyInstance()`

### cglib动态代理

* 通过继承业务类，生成的动态代理为业务类子类，通过重写业务方法进行代理
* 比JDK创建的动态代理性能高，但创建代理对象耗费时间多，对于单例对象可用cglib
* 因采用动态创建子类的方法，对于final方法无法进行代理
  * 子类重写了被代理类中所有非final方法，在子类中采用方法拦截技术拦截所有父类方法的调用，顺势植入横切逻辑

### AOP中创建的是哪种代理（JDK或CGLIB）

* 通过`createAopProxy()`方法决定创建何种proxy
  * 若目标类是接口类（目标实现了接口），则用JDKProxy
  * 其他情况用cglib

## 总结

* `@EnableAspectJAutoProxy `开启AOP功能
* `@EnableAspectJAutoProxy`会给容器注册一个组件AnnotationAwareAspectJAutoProxyCreator
* AnnotationAwareAspectJAutoProxyCreator是一个后置处理器
* 容器的创建流程
  * `registerBeanPostProcessors()`注册后置处理器，创建AnnotationAwareAspectJAutoProxyCreator
  * finishBeanFactoryInitialization()初始化剩下的单实例bean
    * 创建业务逻辑组件和切面组件
    * AnnotationAwareAspectJAutoProxyCreator拦截组件的创建过程
    * 组件创建完成之后，判断组件是否需要增强
      * 是：切面的通知方法，包装成增强器（Advisor）；给业务逻辑组件创建一个代理对象（cglib）
* 执行目标方法
  * 代理对象执行目标方法
  * CglibAopProxy.intercept()
    * 得到目标方法的拦截器链（增强器包装成拦截器MethodInterceptor）
    * 利用拦截器的链式机制，依次进入每一个拦截器执行
    * 效果：
      * 正常执行： 前置通知-->目标方法-->后置通知-->返回通知
      * 出现异常：前置通知-->目标方法-->后置通知-->异常通知

# 事务

## [事务的实现方法](https://www.ibm.com/developerworks/cn/java/j-master-spring-transactional-use/index.html)

* 编程式事务
  * 对于POJO应用来说是唯一选择，需在代码中调用`beginTransaction()` `commit()` `rollback()`
* 基于TransactionalProxyFactoryBean的声明式事务管理
* 基于@Transactional的声明式事务管理
* 基于Aspectj AOP配置事务

* 环境搭建
  * 导入相关依赖
    * 数据源，数据驱动，Spring-jdbc
  * 配置数据源，JdbcTemplate(Spring提供的简化数据库扣伯的工具)操作数据
  * 给方法上标注@Tranactional表示当前方法是一个事务方法
  * @EnableTransactionManagement 开启基于注解的事务管理功能
  * 配置事务管理器控制事务

## 事务传播级别

* `PROPAGATION_REQUIRED` ， 默认的 spring 事务传播级别
  * 若上下文中已存在事务，则加入事务，若不存在事务，则新建事务执行
* ` PROPAGATION_SUPPORTS`
  * 若存在事务,则加入事务,若不存在,则不用事务
* `PROPAGATION_MANDATORY `
  * 要求上下文中必须存在事务，否则抛出异常
* ` PROPAGATION_REQUIRES_NEW `
  * 每次都要一个新事务，且同时将上下文中事务挂起，当前事务完成后，上下文事务恢复再执行
* `PROPAGATION_NOT_SUPPORTED `
  * 上下文中存在事务，则挂起事务，执行当前逻辑，结束后恢复上下文中事务
* `PROPAGATION_NEVER`
  * 上下文中不能存在事务，一旦有事务，就抛出runtime异常，强制停止执行
* `PROPAGATION_NESTED`
  * 若上下文中存在事务，则嵌套事务执行，若不存在事务，则新建事务

## 事务原理

* 通过TransactionManagementConfigurationSelector给容器导入组件

  ```java
  @Import(TransactionManagementConfigurationSelector.class)
  ```

# ApplicationListener

监听容器中发布的事件，完成事件驱动开发

- 监听事件

  - ApplicationListener抽象类

  - @EventListener(class={ApplicationEvent.class})

    ```java
    @EventListener(class={ApplicationEvent.class})
    public void listen(ApplicationEvent event){
        System.out.println("UserService..监听到的事件"+event);
    }
    ```

    

- 监听原理

  - ContextRefreshedEvent事件

    - 容器创建对象,refresh();

    - finishRefresh() 容器刷新完成

    - publishEvent(new ContextRefreshedEvent(this))

      - 事件发布流程

        - 获取事件多播器:getApplicationEventMulicaster()

        - multicastEvent派发事件

        - 获取到所有的ApplicationListener

          ```java
          for (final ApplicationListener<?> listener : getApplicationListeners(event, type)) {
          			Executor executor = getTaskExecutor();
              //如果有executor,可以支持使用Executor进行异步派发
          			if (executor != null) {
          				executor.execute(new Runnable() {
          					@Override
          					public void run() {
          						invokeListener(listener, event);
          					}
          				});
          			}
              //否则同步方式直接执行listener方法
          			else {
          				invokeListener(listener, event);
          			}
          		}
          ```

        - 拿到listener回调onApplicationEvent方法

          ```java
          listener.onApplicationEvent(event);
          ```

  - 容器关闭事件

    ```java
    applicationContext.close()
    ```

    - contextCloseEvent

  - 事件多播器获取

    - 容器创建对象refresh()

    - initApplicationEventMulticaster() 

      ```java
      protected void initApplicationEventMulticaster() {
      		ConfigurableListableBeanFactory beanFactory = getBeanFactory();
          //先去容器中找有没有id="applicationEventMulticaster"的组件
      		if (beanFactory.containsLocalBean(APPLICATION_EVENT_MULTICASTER_BEAN_NAME)) {
      			this.applicationEventMulticaster =
      					beanFactory.getBean(APPLICATION_EVENT_MULTICASTER_BEAN_NAME, ApplicationEventMulticaster.class);
      			if (logger.isDebugEnabled()) {
      				logger.debug("Using ApplicationEventMulticaster [" + this.applicationEventMulticaster + "]");
      			}
      		}
      		else {
      			this.applicationEventMulticaster = new SimpleApplicationEventMulticaster(beanFactory);
      
                  //如果没有则注册一个,并加入到容器中
                  beanFactory.registerSingleton(APPLICATION_EVENT_MULTICASTER_BEAN_NAME, this.applicationEventMulticaster);
      			if (logger.isDebugEnabled()) {
      				logger.debug("Unable to locate ApplicationEventMulticaster with name '" +
      						APPLICATION_EVENT_MULTICASTER_BEAN_NAME +
      						"': using default [" + this.applicationEventMulticaster + "]");
      			}
      		}
      	}
      ```

  - 容器中有哪些监听器

    - 容器创建对象 refresh()

    - 从容器中拿到所有的监听器,把他们注册到applicationEventMulticaster中

      ```java
      String[] listenerBeanNames = getBeanNamesForType(ApplicationListener.class, true, false);
      for (String listenerBeanName : listenerBeanNames) {		getApplicationEventMulticaster().addApplicationListenerBean(listenerBeanName);
      		}
      ```

  - @EventListener原理--SmartInitializingSingleton原理

    - IOC容器创建对象并刷新容器refresh()

    - 初始化所有的单实例(non-lazy-init)Bean

      - finishBeanFactoryInitialization(beanFactory);初始化剩下的单实例Bean

        - 先创建所有的单实例Bean: getBean()

          ```java
          //获取所有创建好的单实例Bean
          for (String beanName : beanNames) {
          			Object singletonInstance = getSingleton(beanName);
              //判断是否是SmartInitializingSingleton类型的
          			if (singletonInstance instanceof SmartInitializingSingleton) {
          				final SmartInitializingSingleton smartSingleton = (SmartInitializingSingleton) singletonInstance;
          				if (System.getSecurityManager() != null) {
          					AccessController.doPrivileged(new PrivilegedAction<Object>() {
          						@Override
          						public Object run() {
          							smartSingleton.afterSingletonsInstantiated();
          							return null;
          						}
          					}, getAccessControlContext());
          				}
          				else {
                              //如果是调用该方法
          					smartSingleton.afterSingletonsInstantiated();
          				}
          			}
          		}
          ```


# Spring中设计模式

## 简单工厂

* 由一个工厂类根据传入的参数动态决定应该创建哪一个产品类
* Spring的BeanFactory是简单工厂模式的体现

## 单例模式

* 提供了全局和访问点BeanFactory
* Spring默认Bean为singleton

## 适配器模式

* Aop中使用Advice增强被代理类的功能

## 代理模式

* 为其他对象提供一种代理以控制对这个对象的访问
* 结构上与装饰器模式类似，但代理模式是控制，更像是一种对功能的限制，而装饰器模式是增加职责
* 在Aop中体现，如JdkDynamicAopProxy和Cglib2AopProxy

## 观察者模式

* 当一对象改变，所有依赖于它的对象都得到通知并更新
* Spring中listener的实现，如ApplicationListener

## 策略模式

* 定义一系列算法，将它们一个个封装起来，使他们可以相互替换，使得算法独立于使用客户
* spring在实例化对象时用到Strategy模式

## 模板方法模式

* 定义一个操作中算法骨架，将一些步骤延迟到子类中
* JdbcTemplate中的execute方法



