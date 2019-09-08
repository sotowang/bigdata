# IOC

## 依赖注入方式

* Setter
* Interface
* Constructor
* Annotation

## Spring IOC容器创建过程

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

**BeanFactory与ApplicationContext比较**

* BeanFactory是Spring框架的基础设施，面向Spring
* ApplicationContext面向使用Spring框架的开发者
* BeanFactory 采用的是延迟加载，第一次getBean的时候才会初始化Bean
* ApplicationContext是对BeanFactory的扩展，提供了更多的功能
  - 国际化处理
  - 事件传递
  - Bean自动装配
  - 各种不同应用层的Context实现

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

## Spring Bean的作用域(scope控制)

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

## Spring Bean的生命周期

bean的生命周期：bean的创建--初始化--销毁的过程，由容器管理bean的生命周期

* 创建
  * 单实例，在容器启动的时候创建对象
  * 多实例，每次获取的时候创建对象
* 初始化
  * 对象创建完成，并赋值好，调用初始化方法
* 销毁
  * 单实例：容器关闭的时候
  * 多实例：容器不会管理这个bean；容器不会调用销毁方法
* 初始化和销毁方法

  * 通过@Bean指定init-method和destroy-method
  * Bean实现InitializingBean
    * 定义初始化逻辑（afterPropertiesSet方法）
  * Bean实现DisposableBean接口
    * 定义销毁逻辑(destroy方法)
  * @PostConstruct
    * bean创建完成并且属性赋值完成，未执行初始化方法
  * @PreDestroy
    * 在容器销毁bean之前通知进行清理工作
  * BeanPostProcessor接口
    * Bean的后置处理器
    * postProcessBeforeInitialization
      * 初始化之前
    * postProcessAfterInitialization
      * 初始化之后
* 自定义组件实现xxxAware
  * 在创建对象的时候，会调用接口元宝的方法注入相关组件：Aware
  * 把Spring底层一些组件注入到自定义的Bean中
  * xxxAware：功能使用xxxProcessor

---

#  Spring AOP

## AOP主要名词概念

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
  * 需要特殊的Java编译器,如AspectJ和AspectWerkz
*  运行时织入
  * Spring采用的方式,通过动态代理的方式,实现简单

## AOP原理

AnnotationAwareAspectJAutoProxyCreator(后置处理器）是EnableAspectJAutoProxy想要往容器中导的组件

会执行以下步骤：

* 获取当前bean的所有增强器（通知方法）Object[] specificInterceptors

  * 找到候选的所有增强器（找到哪些通知方法是需要切入当前bean方法）
  * 获取到能在bean使用的增强器
  * 给增强器排序

* 保存当前bean在advisedBeans中

* 如果当前bean需要增强，创建当前bean的代理对象

  * 获取所有增强器（通知方法）

  * 保存到proxyFactory

  * 创建代理对象：Spring自动决定

    * ```java
      if (targetClass.isInterface() || Proxy.isProxyClass(targetClass)) {
      				return new JdkDynamicAopProxy(config);
      			}
      //在默认情况下，如果目标对象没有实现任何接口，Spring会选择CGLIB代理， 其生成的动态代理对象是目标类的子类。
      ```

    * JdkDynamicAopProxy(config）

      * Jdk动态代理

    * ObjensisCglibAopProxy(config)

      * cglib的动态代理

* 给容器中返回当前组件使用cglib增强了的对象

* 以后容器中获取到的就是这个组件的代理对象，执行目标方法的时候，代理对象就会执行通知方法的流程



**目标方法执行原理**

* 容器中保存了组件的代理对象（cglib增强后的对象），这个对象里面保存了详细信息（比如增强器，目标对象）

  * CglibAopProxy.intercept()

    * 拦截目标方法的执行

  * 根据ProxyFactory对象获取将要执行的目标方法拦截器链

    * List<Object> interceptorList保存所有拦截器（一个国碎的ExposeInvocationInterceptor和4个增强器）
    * 遍历所有的增强器，将其转为Interceptor
    * 将增强器转为List<MethodInterceptor>
      * 如果是MethodInterceptor，直接加入到集合
      * 如果不是使用AdvisorAdapter将增强器转为MethodInterceptor
      * 转换完成返回MethodInterceptor数组

  * 如果没有拦截器链，直接执行目标方法

  * 如果有拦截器链，把需要执行的目标对象，目标方法，拦截器链等信息传入创建一个CglibMethodInvocation对象，并调用proceed方法

    * ```java
      MethodInvocation invocation = new CglibMethodInvocation(proxy, this.target, method, args,this.targetClass, this.adviceChain, methodProxy);
      
      Object retVal = invocation.proceed();
      ```

  * 拦截器链的触发过程

    * 如果没有拦截器执行目标方法，或者拦截器的索引和拦截器数组-1大小一样（指定到了最后一个拦截器）
    * 链式获取每一个拦截器，拦截器执行invoke方法，每一个拦截器等待下一个拦截器执行完成返回以后再来执行；拦截器链的机制，保证通知方法与目标方法的执行顺序。

### 总结

* @EnableAspectJAutoProxy 开启AOP功能
* @EnableAspectJAutoProxy会给容器注册一个组件AnnotationAwareAspectJAutoProxyCreator
* AnnotationAwareAspectJAutoProxyCreator是一个后置处理器
* 容器的创建流程
  * registerBeanPostProcessors()注册后置处理器，创建AnnotationAwareAspectJAutoProxyCreator
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

# 声明式事务

* 环境搭建
  * 导入相关依赖
    * 数据源，数据驱动，Spring-jdbc
  * 配置数据源，JdbcTemplate(Spring提供的简化数据库扣伯的工具)操作数据
  * 给方法上标注@Tranactional表示当前方法是一个事务方法
  * @EnableTransactionManagement 开启基于注解的事务管理功能
  * 配置事务管理器控制事务

## 事务原理

* 通过TransactionManagementConfigurationSelector给容器导入组件

  ```java
  @Import(TransactionManagementConfigurationSelector.class)
  ```

# BeanFactoryPostProcessor

* BeanPostProcessor
  * bean后置处理器，bean创建对象初始化前后进行拦截工作
* BeanFactoryPostProcessor
  * beanFactory的后置处理器
  * 在BeanFactory标准初始化之后调用，所有的bean定义已经保存加载到beanFactory

# BeanDefinitionRegistryPostProcessor

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

          









