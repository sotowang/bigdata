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

