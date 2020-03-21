#  # 和 $的区别

* `#`对传入的数据加双引号，`$`直接解析成sql
* `#`防止sql注入，能用`#`不用`$`
  * 原sql： `select count(*) from admin where username = 'test' and password = 'test'`
  * [注入](https://blog.csdn.net/u012436758/article/details/53871290)： `select count(*) from admin where username = ' 'or 1=1-- ' and password = ' ' "`sql在招行时，`"--"`将`and`及之后的语句都注释了
* `$`方式一般用于传入数据库对象，例如传入表名
* 简单说#{}是经过预编译的,是安全的,而${}是未经过预编译的,仅仅是取变量的值,是非安全的,存在sql注入。
*   只能`${}的情况,order by、like 语句只能用${}`了,用#{}会多个' '导致sql语句失效.此外动态拼接sql也要用${}。

# 一级缓存和二级缓存

![img](https://img-blog.csdn.net/20150726164148424?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

* 一级缓存（`SqlSession`级别）
  * 同一`SqlSession`执行相同`sql`，第一次会将结果写入缓存（内存`HashMap`）
  * 若执行commit操作（insert,update,delete），清空一级缓存
* 二级缓存（Mapper级别）
  * 多个`SqlSession`共用二级缓存，作用域为Mapper的同一个namespace
  * 默认没开二级缓存，需在setting全局参数中配置开启二级缓存
* 使用顺序
  * 二级缓存->一级缓存->数据库
* 缓存实现
  * `MyBatis`自身缓存实现
  * 用户自定义Cache接口实现
  * 跟第三方内存缓存库和集成
* 缓存置换策略
  * LRU
  * FIFO
* 主要设计模式
  * 装饰器模式

# [分页](https://blog.csdn.net/chenbaige/article/details/70846902)

* 数组分页(逻辑分页)

  * 获取所有满足条件的记录存在临时数组中，通过`List`的`subList()`分页

* `sql`语句分页(物理分页)

  * 在`sql`后面加`limit`分页语句

* `RowBounds`分页

  * 和数组分页原理一样,只是不需要我们自己实现分页原理

  * 内存消耗大,可能导致性能差,OOM

  * ```java
    /加入RowBounds参数
    public List<UserBean> queryUsersByPage(String userName, RowBounds rowBounds);
    ```

* 拦截器分页

## 逻辑分页和物理分页

* 区别
  * 物理每次都访问数据库,负担大,逻辑只访问一次,占内存大
  * 物理实时性高,逻辑一次访问全部数据.实时性差
* 适用
  * 逻辑: 数据量小,数据稳定
  * 物理:数据量大, 更新频繁

# 延迟加载

* 先加载主要数据,如果需要关联数据,再加载

* 优点

  * 避免资源浪费,提升查询速度

* 开启

  ```xml
  <settings>
  	<setting name="lazyLoadingEnabled" value="true"/> 
      <setting name="aggressiveLazyLoading" value="false"/> 
  </settings>
  //lazyLoadingEnabled 这个是开启懒加载的全局配置 //aggressiveLazyLoading 这个属性必须改为false 否则不生效
  ```

# MyBatis和 Hibernate的区别

* MyBatis半自动,Hibernate全自动
  * Hibernate完全可以通过对象关系模型(ORM)实现对数据库操作,拥有完整的JavaBean对象与数据库的映射结构自动生成SQL
  * MyBatis仅有基本字段映射,对象数据及关系还需定制SQL实现管理
* Hibernate数据库移植性好
  * MyBatis需要手写SQL,耦合度高
* Hibernate拥有完整日志系统
  * 包括:SQL记录,关系异常,优化警告,缓存提示,脏数据警告
  * MyBatis除了基本记录功能外,功能薄弱
* MyBatis使用简单,但需要关心更多细节
* MyBatis SQL优化较好(公司选择原因)
  * MyBatis因sql写在xml里,Hibernate很多是自动生成的,无法直接维护sql
* Hibernate安全性要好,MyBatis存在SQL注入问题

# 有哪些执行器（Executor）

# 分页插件的实现原理

# 如何编写一个自定义插件



