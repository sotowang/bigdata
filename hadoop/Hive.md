# 什么是Hive

* 构建在Hadoop上的数据仓库,殷SQL查询转为一系列Hadoop集群中运行的MapReduce作业,是MapReduce更高层次的抽象
* Hive将数据组织为表,使HDFS上的数据有了结构,元数据即表的模式,都存储在名为metastore的数据库中

# 什么是UDF

* Hive提供的函数功能满足不了业务的需要,需要写UDF辅助完成
* UDF是执行过程是地Hive转换成mapreduce程序后,执行Java方法,类似于像Mapreduce执行过程中加入一个插件,方便扩展
* UDF只能实现一进一出的操作,若实现多进一出,则需要UDAF

# UDF类型

* UDF
  * 操作单个数据行,产生单个数据行
* UDAF
  * 操作多个数据行,产生一个数据行
* UDTF
  * 操作一个数据行,产生多个数据行一个表作为输出

# 如何构建UDF

* 步骤
  * 继承UDF或者UDAF或者UDTF,实现特定方法
  * 将写好的类打包为jar
  * 进扩到Hive外壳环境.利用add jar xxx.jar 注册jar文件
  * 将该类起一个别名
    * `create temporary function mylength as 'com.xxx.StringLength';`这里注意UDF只是为这个Hive会话临时定义
  * 在select 中使用mylength()
* 注意
  * UDF
    * UDF必须继承`org.apache.hadoop.hive.ql.exec.UDF`
    * 必须包含有`evaluate()`方法,但该方法并不存在于UDF中,`evaluate`的参数由用户自定义,使用时Hive会调用UDF的`evaluate()`方法
  * UDAF
    * UDAF必须继承`org.apache.hadoop.hive.ql.exec.UDAF`
    * 必须包含至少一个实现了`org.apache.hadoop.hive.ql.exec`的静态类,如常见的实现了`UDAFEvaluator`
    * 一个计算函数须实现5个方法
      * `Init()`负责初始化计算函数并重设其内部状态,一般是重设内部字段,一般在静态类中定义一个内部字段存放最终结果
      * `iterate()`每一次对一个新值进行聚集计算时会调用该方法,计算函数会根据聚焦计算结果更新内部状态,当输入值合法或正确计算了则返回true
      * `terminatePartia()`Hive需要部分聚集结果的时候会调用该方法,必须返回一个封装了聚集计算当前状态的对象
      * `merge()`Hive进行合并一个部分聚集和另一个部分聚集的时候会调用该方法
      * `terminate()`Hive最终聚集结果的时候会调用该方法,计算函数需要把状态作为一个值返回给用户
    * 部分聚集结果的数据类型和最终结果的数据类型可以不同