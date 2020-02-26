# Spring Boot自动配置实现

* Spring Boot 项目启动注解:@SpringBootApplication
  * @Configuration
  * @ComponentScan
  * @EnableAutoConfiguration
    * 实现自动配置入口
    * 该注解通过@Import注解导入了AutoConfigurationImportSelector,在该类中加载META-INF/spring.factories的配置信息
    * 筛选出以EnableAutoConfiguration为key的数据,加载到IOC容器中

# Spring Cache三种常用的缓存注解和意义

* @Cacheable
  * 声明方法是可缓存的,将结果存储到缓存中,后续调用直接取值
* @CachePut
  * 每次都会执行该方法,将执行结果以键值对的形式存入指定缓存
* @CacheEvict
  * 标在需要清除缓存元素的方法或类上

# JPA 和 Hibernate 有哪些区别？

JPA是一种规范,只提供一些相关接口,不能直接使用,Hibernate是JPA的一个实现集

# JPA 可以支持动态 SQL 吗？

JPA根据实体类注解创建对应表和字段,若需要动态创建表或字段,

* 需要动态构建对应的实体类
* 再重新调用JPA刷新整个Entity

# [Spring循环依赖](https://blog.csdn.net/github_38687585/article/details/82317674)

* Spring循环依赖的理论依据
  * Java基于引用传递，当我们获取到对象的引用时，对象的field和属性是可以延后设置的
  * Spring单例对象的初始化过程：实例化，填充属性，初始化
    * `createBeanInstance`实例是调用对应构造方法，Spring中xml中的preperty没有进
    * `populate`填充属性，对Spring xml中的属性进行填充
    * `initializeBean`,调用Spring xml中指定的init方法或AfterPropertiesSet方法
  * 循环依赖发生在第一步和第二步

* 循环依赖产生的结果
  * Spring加载Bean时会以正常顺序创建Bean，当存在循环依赖时，Spring将无法决定创建哪个Bean
  * Spring将产生`BeanCurrentlyInCreationException`异常
  * 若双方使用setter注入或属性值注入，Spring可自动解决循环依赖问题
  * 若双方使用构造函数注入或**主Bean**（Spring先加载的Bean）使用构造函数注入，则无法解决依赖注入，程序无法启动

* 解决方案
  * 重新设计结构，消除循环依赖
  * 使用注解  `@Lazy `懒加载（可能会掩盖问题）
  * 使用`Setter/Field`注入，当依赖最终被使用时才进行注入
  * 使用`PostConstruct`
  * 实现`ApplicationContextAware`与`InitializingBean`

## 三级缓存

```java
/** Cache of singleton objects: bean name --> bean instance */

private final Map<String, Object> singletonObjects = new ConcurrentHashMap<String, Object>(256);

/** Cache of singleton factories: bean name --> ObjectFactory */

private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<String, ObjectFactory<?>>(16);

/** Cache of early singleton objects: bean name --> bean instance */

private final Map<String, Object> earlySingletonObjects = new HashMap<String, Object>(16);
```

* 指
  * singletonObjects`单例对象Cache
  * `singletonFactories`单例工厂Cache
  * `earlySingletonObjects`提前曝光的单例对象cache

* 三级缓存用法

  * `protected Object getSingleton(String beanName, boolean allowEarlyReference) `

  * Spring从`singletonObjects`(一级缓存)中获取
  * 若获取不到且对象在创建中，则从`earlySingletonObjects`(二级缓存)中获取
  * 若获取不到则从`singletonFactories`（三级缓存）中获取，若获取到了则将`singletonObject`放入到`earlySingletonObjects`,即将三级缓存升到二级缓存中

* 为何需要三级而不是二级
  * 将三级缓存放到二级缓存时，会是否有`SmartInstantiationAwareBeanPostProcessor`这样的后置处理器，这里是给用户提供接口扩展的（循环使用？）