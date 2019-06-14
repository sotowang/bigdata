# Hibernate中默认采用延迟加载的情况

* 当调用session上的load()加载一个实体时，会采用延迟加载。
* 当session加载某个实体时，会对这个实体中的集合属性值采用延迟加载
* 当session加载某个实体时，会对这个实体所有单端关联的另一个实体对象采用延迟加载。



# Hibernate 中 get()和load() 的区别

* get()采用立即加载方式,而load()采用 延迟加载
* get()方法执行的时候,会立即向数据库发出查询语句, 而load()方法返回的是一个代理(此代理中只有一个id属性,只有等真正使用该对象属性的时候,才会发出 sql语句
* 如果数据库中没有对应的记录,get()方法返回的是null.而load()方法出现异常ObjectNotFoundException

