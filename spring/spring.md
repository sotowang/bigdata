# [对于Spring IOC及AOP的理解](https://mp.weixin.qq.com/s/wcK2qsZxKDJTLIGqEIyaNg)

## IOC

* IoC（Inverse of Control:控制反转）是一种**设计思想**，就是 **将原本在程序中手动创建对象的控制权，交由Spring框架来管理。** IoC 在其他语言中也有应用，并非 Spirng 特有。 **IoC 容器是 Spring 用来实现 IoC 的载体， IoC 容器实际上就是个Map（key，value）,Map 中存放的是各种对象。**
* 将对象之间的相互依赖关系交给 IoC 容器来管理，并由 IoC 容器完成对象的注入。这样可以很大程度上简化应用的开发，把应用从复杂的依赖关系中解放出来。 **IoC 容器就像是一个工厂一样，当我们需要创建一个对象的时候，只需要配置好配置文件/注解即可，完全不用考虑对象是如何被创建出来的。** 在实际项目中一个 Service 类可能有几百甚至上千个类作为它的底层，假如我们需要实例化这个 Service，你可能要每次都要搞清这个 Service 所有底层类的构造函数，这可能会把人逼疯。如果利用 IoC 的话，你只需要配置好，然后在需要的地方引用就行了，这大大增加了项目的可维护性且降低了开发难度。
* Spring 时代我们一般通过 XML 文件来配置 Bean，后来开发人员觉得 XML 文件来配置不太好，于是 SpringBoot 注解配置就慢慢开始流行起来。

### IOC初始化过程

* **Spring IoC的初始化过程：**

  ![Spring IoC的初始化过程](https://mmbiz.qpic.cn/mmbiz_png/iaIdQfEric9TxiaKwgUUHQX0aVpNnuopm5wZ4f7md6eOwW9ZSpk2LV0C5FNYLlBgCIE2pFC6kswRIVsUc8z9jicBxA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

## [AOP](https://mp.weixin.qq.com/s/wcK2qsZxKDJTLIGqEIyaNg)

* AOP(Aspect-Oriented Programming:面向切面编程)能够将那些与业务无关，**却为业务模块所共同调用的逻辑或责任（例如事务处理、日志管理、权限控制等）封装起来**，便于**减少系统的重复代码**，**降低模块间的耦合度**，并**有利于未来的可拓展性和可维护性**。

* <span style='color:red'>Spring AOP就是基于动态代理的</span>，如果要代理的对象，实现了某个**接口，那么Spring AOP会使用JDK Proxy**，去创建代理对象，而对于**没有实现接口的对象，就无法使用 JDK Proxy 去进行代理了，这时候Spring AOP会使用Cglib** ，这时候Spring AOP会使用 Cglib 生成一个被代理对象的子类来作为代理，如下图所示：

  ![img](https://mmbiz.qpic.cn/mmbiz_jpg/iaIdQfEric9TxiaKwgUUHQX0aVpNnuopm5wIpBukqk0x4zl3jOOuhf7kUnzXkgRtaMF4CGKBnRP0axAeTc4TkbWkQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

* 当然你也可以使用 AspectJ ,Spring AOP 已经集成了AspectJ ，**AspectJ 应该算的上是 Java 生态系统中最完整的 AOP 框架了**。


### Spring AOP 和 AspectJ AOP 区别

* **Spring AOP 属于运行时增强，而 AspectJ 是编译时增强。** Spring AOP 基于代理(Proxying)，而 AspectJ 基于字节码操作(Bytecode Manipulation)。

* Spring AOP 已经集成了 AspectJ ，AspectJ 应该算的上是 Java 生态系统中最完整的 AOP 框架了。AspectJ 相比于 Spring AOP 功能更加强大，但是 Spring AOP 相对来说更简单，

* 如果我们的切面比较少，那么两者性能差异不大。但是，<span style='color:red'>当切面太多的话，最好选择 AspectJ ，它比Spring AOP 快很多</span>。

## [Bean](https://mp.weixin.qq.com/s/wcK2qsZxKDJTLIGqEIyaNg)

### bean 的作用域(scope控制)

- singleton : 唯一 bean 实例，Spring 中的 bean **默认**都是单例的。
- prototype : 每次请求都会创建一个新的 bean 实例。
- request : 每一次HTTP请求都会产生一个新的bean，该bean仅在当前HTTP request内有效。
- session : 每一次HTTP请求都会产生一个新的 bean，该bean仅在当前 HTTP session 内有效。
- global-session：全局session作用域，仅仅在基于portlet的web应用中才有意义，**Spring5已经没有了**。Portlet是能够生成语义代码(例如：HTML)片段的小型Java Web插件。它们基于portlet容器，可以像servlet一样处理HTTP请求。但是，与 servlet 不同，每个 portlet 都有不同的会话

### 单例 bean 的线程安全问题

* 大部分时候我们并没有在系统中使用多线程，所以很少有人会关注这个问题。单例 bean 存在线程问题，主要是因为当多个线程操作同一个对象的时候，对这个对象的非静态成员变量的写操作会存在线程安全问题。
* 常见的有两种解决办法：
  1. 在Bean对象中尽量避免定义可变的成员变量（不太现实）。
  2. <span style="color:red">在类中定义一个ThreadLocal成员变量，将需要的可变成员变量保存在 ThreadLocal 中（推荐的一种方式）。</span>

### Bean生命周期

- Bean 容器找到配置文件中 Spring Bean 的定义。
- Bean 容器利用 Java Reflection API 创建一个Bean的实例。
- 如果涉及到一些属性值 利用 `set()`方法设置一些属性值。
- 如果 Bean 实现了 `BeanNameAware` 接口，调用 `setBeanName()`方法，传入Bean的名字。
  - 如果 Bean 实现了 `BeanClassLoaderAware` 接口，调用 `setBeanClassLoader()`方法，传入 `ClassLoader`对象的实例。
  - 如果Bean实现了 `BeanFactoryAware` 接口，调用 `setBeanClassLoader()`方法，传入 `ClassLoade` r对象的实例。
  - 与上面的类似，如果实现了其他 `*.Aware`接口，就调用相应的方法。
- 如果有和加载这个 Bean 的 Spring 容器相关的 `BeanPostProcessor` 对象，执行`postProcessBeforeInitialization()` 方法
- 如果Bean实现了`InitializingBean`接口，执行`afterPropertiesSet()`方法。
- 如果 Bean 在配置文件中的定义包含 init-method 属性，执行指定的方法。
- 如果有和加载这个 Bean的 Spring 容器相关的 `BeanPostProcessor` 对象，执行`postProcessAfterInitialization()` 方法
- 当要销毁 Bean 的时候，如果 Bean 实现了 `DisposableBean` 接口，执行 `destroy()` 方法。
- 当要销毁 Bean 的时候，如果 Bean 在配置文件中的定义包含 destroy-method 属性，执行指定的方法。

与之比较类似的中文版本:

![img](https://mmbiz.qpic.cn/mmbiz_jpg/iaIdQfEric9TxiaKwgUUHQX0aVpNnuopm5wibfdy0aVDtF5TS6Zcg8zSghhliapibEyzibtCibsu3a1HTYic2SZ07a7icKJQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

### [@Component 和 @Bean 的区别](https://mp.weixin.qq.com/s/wcK2qsZxKDJTLIGqEIyaNg)

1. 作用对象不同: `@Component` 注解作用于类，而`@Bean`注解作用于方法。
2. `@Component`通常是通过类路径扫描来自动侦测以及自动装配到Spring容器中（我们可以使用 `@ComponentScan` 注解定义要扫描的路径从中找出标识了需要装配的类自动装配到 Spring 的 bean 容器中）。`@Bean` 注解通常是我们在标有该注解的方法中定义产生这个 bean,`@Bean`告诉了Spring这是某个类的示例，当我需要用它的时候还给我。
3. `@Bean` 注解比 `Component` 注解的自定义性更强，而且很多地方我们只能通过 `@Bean` 注解来注册bean。**比如当我们引用第三方库中的类需要装配到 `Spring`容器时，则只能通过 `@Bean`来实现。**

### [如何判断bean是否加载完](https://www.cnblogs.com/0201zcr/p/4720803.html)

* **onApplicationEvent**（）方法，**在Spring容器将所有的Bean都初始化完成之后，就会执行该方法**

* **应用场景**：很多时候我们想要在某个类加载完毕时干某件事情，但是使用了spring管理对象，我们这个类引用了其他类（可能是更复杂的关联），所以当我们去使用这个类做事情时发现包空指针错误，这是因为我们这个类有可能已经初始化完成，但是引用的其他类不一定初始化完成，所以发生了空指针错误，解决方案如下： 

  1、写一个类继承spring的`ApplicationListener`监听，并监控`ContextRefreshedEvent`事件（容易初始化完成事件） 

  2、定义简单的`bean：<bean id="beanDefineConfigue" class="com.creatar.portal.webservice.BeanDefineConfigue"></bean>  `

## [Spring中设计模式](https://mp.weixin.qq.com/s/wcK2qsZxKDJTLIGqEIyaNg)

### 简单工厂

- 由一个工厂类根据传入的参数动态决定应该创建哪一个产品类
- Spring的BeanFactory是简单工厂模式的体现

### 单例模式

- 提供了全局和访问点BeanFactory
- Spring默认Bean为singleton

### 适配器模式

- Spring AOP 的增强或通知(Advice)使用到了适配器模式、spring MVC 中也是用到了适配器模式适配`Controller`。

### 代理模式

- 为其他对象提供一种代理以控制对这个对象的访问
- 结构上与装饰器模式类似，但代理模式是控制，更像是一种对功能的限制，而装饰器模式是增加职责
- **在Aop中体现**，如JdkDynamicAopProxy和Cglib2AopProxy

### 观察者模式

- 当一对象改变，所有依赖于它的对象都得到通知并更新
- Spring中listener的实现，如ApplicationListener

### 策略模式

- 定义一系列算法，将它们一个个封装起来，使他们可以相互替换，使得算法独立于使用客户
- spring在实例化对象时用到Strategy模式

### 模板方法模式

- 定义一个操作中算法骨架，将一些步骤延迟到子类中
- JdbcTemplate中的execute方法

### **包装器设计模式** 

- 我们的项目需要连接多个数据库，而且不同的客户在每次访问中根据需要会去访问不同的数据库。这种模式让我们可以根据客户的需求能够动态切换不同的数据源。

## [事务](https://mp.weixin.qq.com/s/wcK2qsZxKDJTLIGqEIyaNg)

### [Spring 管理事务的方式](https://mp.weixin.qq.com/s/wcK2qsZxKDJTLIGqEIyaNg)

1. 编程式事务，在代码中硬编码。(不推荐使用)
2. <span style="color:red">声明式事务，在配置文件中配置（推荐使用）</span>
   1. 基于XML的声明式事务
   2. 基于注解的声明式事务

### [Spring 事务中的隔离级别](https://mp.weixin.qq.com/s/wcK2qsZxKDJTLIGqEIyaNg)

* default
  * 使用后端数据库默认的隔离级
* 未提交读
  * 最低的隔离级别，允许读取尚未提交的数据变更，**可能会导致脏读、幻读或不可重复读**
* 已提交读
  * 对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，**可以阻止脏读和不可重复读，但幻读仍有可能发生。**
* 可序列化
  * 最高的隔离级别，完全服从ACID的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，**该级别可以防止脏读、不可重复读以及幻读**。但是这将严重影响程序的性能。通常情况下也不会用到该级别。

### 事务传播行为

* 支持<span style="color:red">当前</span>事务的情况
  * `PROPAGATION_REQUIRED` ， 默认的 spring 事务传播级别
    - 若上下文中已存在事务，则加入事务，若不存在事务，则新建事务执行
  * ` PROPAGATION_SUPPORTS`
    - 若存在事务,则加入事务,若不存在,则不用事务
  * `PROPAGATION_MANDATORY `
    * 要求上下文中必须存在事务，否则抛出异常
* 不支持<span style="color:red">当前</span>事务的情况
  * ` PROPAGATION_REQUIRES_NEW `
    - 每次都要一个新事务，且同时将上下文中事务挂起，当前事务完成后，上下文事务恢复再执行
  * `PROPAGATION_NOT_SUPPORTED `
    - 上下文中存在事务，则挂起事务，执行当前逻辑，结束后恢复上下文中事务
  * `PROPAGATION_NEVER`
    - 上下文中不能存在事务，一旦有事务，就抛出runtime异常，强制停止执行
* 其他情况
  * `PROPAGATION_NESTED`
    - 若上下文中存在事务，则嵌套事务执行，若不存在事务，则新建事务

### @Transactional(rollbackFor = Exception.class)注解

* 我们知道：Exception分为运行时异常RuntimeException和非运行时异常。事务管理对于企业应用来说是至关重要的，即使出现异常情况，它也可以保证数据的一致性。

* 当`@Transactional`注解作用于类上时，**该类的所有 public 方法将都具有该类型的事务属性**，同时，我们也可以在方法级别使用该标注来覆盖类级别的定义。如果类或者方法加了这个注解，那么这个类里面的方法抛出异常，就会回滚，数据库里面的数据也会回滚。

* 在`@Transactional`注解中如果不配置`rollbackFor`属性,那么事物只会在遇到`RuntimeException`的时候才会回滚,加上`rollbackFor=Exception.class`,可以让事物在遇到非运行时异常时也回滚。



### 事务原理

- 通过TransactionManagementConfigurationSelector给容器导入组件

  ```java
  @Import(TransactionManagementConfigurationSelector.class)
  ```

## [如何使用JPA在数据库中非持久化一个字段](https://mp.weixin.qq.com/s/wcK2qsZxKDJTLIGqEIyaNg)

* 一般使用后面两种方式比较多，我个人使用注解的方式比较多。

```java
static String transient1; // not persistent because of static

final String transient2 = “Satish”; // not persistent because of final

transient String transient3; // not persistent because of transient

@Transient
String transient4; // not persistent because of @Transient
```



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

# [Spring中同一接口有多个实现类时如何注入](https://blog.csdn.net/u010476994/article/details/80986435)

* @Autowired

  * @Autowired 的注入方式是 **byType** 注入， 当要注入的类型在容器中存在多个时，Spring是不知道要引入哪个实现类的，所以会报错。

* @Resource 

  * 默认是按照 byName 的方式注入的， 如果通过 byName 的方式匹配不到，再按 byType 的方式去匹配

    ``` java
    public class AnimalController {
        @Resource(name="dogImpl")        //实现类1中 @Service注解中标定的名称
        private IAnimal dogImpl;
        ......
    
    ```

* @Qualifier 

  * 注解也是 byName的方式，但是与@Resource 有区别，@Qualifier 使用的是 **类名**,必须和Autowired一起用

  * ```java
    
    public class AnimalController {
        @Qualifier("DaoImpl")        //实现类1的类名。注意区分与@Resource(name="dogImpl") 的区别。
        private IAnimal dogImpl;
    ```



