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



# HBase

- HBase Table组成

Table = RowKey + Family+Column+Timestamp+value

- 数据存储模式

（Table，Rowkey，Family。Column，Timestamp）-> Value

## LSM树存储思想

0级：日志/内存

1级：日志/内存

2级：合并（Compaction）

![深度截图_选择区域_20190524135424.png](https://i.loli.net/2019/05/24/5ce7871df3b5c97238.png)

- Region

  负载均衡和分布式存储的最小单元

- HFile

  存储的最小单元

  Data Block

- WAL

  HLog： 实现WAL的类，一台服务器（RegionServer）只有一个

## HBase Compaction

将小文件合并成大文件，方便管理，打开关闭小文件太耗时。

* MinorCompaction

* MajorCompaction(大合并)

  合并成一个StoreFile

![深度截图_选择区域_20190524144412.png](https://i.loli.net/2019/05/24/5ce792cc1300183045.png)

* 触发时机

## 数据存储流程

### 写数据流程

1. HBase Client请求zookeeper，确定meta表（HBase元数据表）所在regionSever地址
2. 根据rowkey找到归属的regionserver
3. 提交put，delete请求，put默认放到本地buffer（默认2M），超过阈值将提交至服务器
4. 加锁（RowLock）保证行的原子操作，把数据分别写到HLog和MemStore上一份，释放行锁，日志同步
5. MemStore达到一个阈值（64M）后则把数据刷成一个StoreFile文件。若MemStore中的数据有丢失，则可以总HLog上恢复
6. 当多个StoreFile文件达到一定的大小后，会触发Compact合并操作，合并为一个StoreFile，这里同时进行版本的合并和数据删除。
7. 当Compact后，逐步形成越来越大的StoreFIle后，会触发Split操作，把当前的StoreFile分成两个，这里相当于把一个大的region分割成两个region

###  读数据流程

1. HBase请求zookeeper，从zookeeper中找到meta表region的位置，然后读取meta表中的数据。meta中又存储了用户表的region信息。
2. 根据namespace、表名和rowkey在meta表中找到对应的region信息
3. 找到这个region对应的regionserver
4. 查找对应的region
5. 先从MemStore找数据，如果没有，再到StoreFile上读(为了读取的效率)。

##  api

### put(Put put)

* 构造方法

  Put(byte[] row)

* 填充值

  Put add(byte[] family,byte[] qualifier,byte[] value)

### delete(Delete delete)

* 构造方法

  Dlelete(byte[] row)

* 填充值

  Delete deleteFamily(byte[] family)

  Delete deleteColumns(byte[] family,byte[] qualifer)

### Result get(Get get)

* 构造方法

  Get(byte[] row)

* 填充值

  Get addFamily(byte[] family)

  Get addColumn(byte[] family,byte[] qualifier)

  Get setMaxVersions()

  Get setTimeRange(long minStamp,long maxStamp)

 

 

 

 

 

 

 

 



















