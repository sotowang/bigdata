# hbase 的特点是什么

 (1) Hbase一个分布式的基于列式存储的数据库,基于Hadoop的hdfs存储，zookeeper进行管理。

(2) Hbase适合存储半结构化或非结构化数据，对于数据结构字段不够确定或者杂乱无章很难按一个概念去抽取的数据。

(3) Hbase为null的记录不会被存储.

(4)基于的表包含rowkey，时间戳，和列族。新写入数据时，时间戳更新，同时可以查询到以前的版本.

(5) hbase是主从架构。hmaster作为主节点，hregionserver作为从节点。

为了运行Hbase，Zookeeper是必须的，zookeeper 是一个用来进行分布式协调的服务，这些**服务包括配置服务，维护元信息和命名空间服务**。

# Hbase和hive 有什么区别

Hive和Hbase是两种基于Hadoop的不同技术

Hive是一种类SQL 的引擎，并且运行MapReduce 任务，

Hbase 是一种在Hadoop之上的NoSQL的Key/vale数据库。

Hive 可以用来进行统计查询，HBase 可以用来进行实时查询，数据也可以从Hive 写到Hbase，设置再从Hbase 写回Hive。

