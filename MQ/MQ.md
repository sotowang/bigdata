# 为何要用MQ系统

* 解耦
  * 通过Pub/Sub发布订阅消息模型,使系统解耦

    ![img](https://user-gold-cdn.xitu.io/2018/12/14/167aa142fc55e9a3?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

* 异步 

  ![img](https://user-gold-cdn.xitu.io/2018/12/14/167aa14316752f9e?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

* 削峰
  ![img](https://user-gold-cdn.xitu.io/2018/12/14/167aa14336c8057f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

# 消息队列优缺点

* 优点
  * 解耦
  * 异步
  * 削峰
* 缺点
  * 系统可用性降低
    * 引用外部依赖越多,越容易挂掉
  * 系统复杂度高
    * 重复消费问题
    * 消息丢失问题
    * 消息顺序性
  * 一致性问题
    * A系统处理完返回成功，但若BCD中有没成功，用户会以为成功，导致数据不一致

# MQ(ActiveMQ/RabbitMQ/RocketMQ/Kafka)对比 

* RabbitMQ

  * 吞吐量大（w级）
  * 开源社区活跃
  * Erlang开发，很难看懂源码，依赖于开源社区
* RocketMQ

  * 吞吐量比RabbitMQ大

  * 消息0丢失

  * 阿里开源，社区活跃度弱于RabbitMQ，可能黄掉

  * 分布式易扩展
* Kafka
  * 适于大数据领域
  * 吞吐量高
  * 实时计算，日志采集

​    

![img](https://user-gold-cdn.xitu.io/2018/12/14/167aa14337dccf2e?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

# RocketMQ集群模式 

* 多master

* 多master多slave异步复制模式

* 多master多slave同步双写模式 

* 多master多slave模式

  ![多master多slave模式](https://segmentfault.com/img/bVbcl9A?w=788&h=465)

# 如何保证MQ高可用

* RabbitMQ
  * 普通集群模式
    * 消费者消费数据时，找到一个节点的Queue，该Queue再去寻找拥有数据的节点Queue，增加了Queue之间的网络传输
  * 镜像集群模式
    * 每个Queue都有全部数据，减少了网络IO，写消息时才有网络IO
    * 缺点
      * 非分布式，若Queue数量很大时，会超出机器容量

* RocketMQ
  * Producer与NameServer集群中一个节点(随机)建立长连接,定期从NameServer取Topic路由信息,并向提供Topic服务的Broker Master建立长连接,且定时向Broker发送心跳,Producer只能将消息发送到 Broker Master
  * Consumer同时和提供Topic服务的Master和Slave建立长连接,既可从Broker Master订阅消息,也可从Broker Slave订阅消息
* Kafka
  * 分布式架构，多个partition副本，选举一个leader,生产者/消费者从leader生产/消费数据，leader负责数据同步

# 如何保证不被重复消费(幂等性)

* 为什么造成重复消费

  * 正常下,消费者消费完毕时会发确认消息给消息队列 ,消息队列将消息删除
    * RabbitMQ发送ACK确认消息
    * RocketMQ返回CONSUME_SUCCESS成功标志
    * Kafka每个消息有一个offset,kafka消费过后,提交offset,让消息队列知道已消费过

* 造成重复消费的原因 

  * 网络传输故障,确认信息没到消息队列,消息队列不知已消费,再次将消息分给其他消费者

* 解决

  * 若用消息做数据库insert,给消息做唯一主键,重复消费会使主键冲突,避免脏数据
  * 若用消息做redis的set操作,set本身幂等,不用解决
  * 准备第三方介质,做消费记录,以redis为例,给消息分配一个全局id,只要消费消息,将<id,message>以K-V形式写入redis,消费前去redis中查询有无消费记录

# 如何保证消息的可靠性

  * 生产者弄丢数据

      * RabbitMQ提供transaction和confirm模式保证生产者不丢消息

          * 发送消息前开启事务(channel.txSelect()),发送过程中出现异常,事务回滚(channel.txRollback()),发送成功则提交事务(channel.txCommit())
          * 缺点:

              * 吞吐量下降 
          * 生产上使用confirm,一旦channel进入confirm,生产上发布的消息都会被指派一个唯一ID,一旦消息被投递,会发ACK给生产者(包含消息ID),生产者知道消息到达;若没能处理该消息,则会发Nack,进行重试

  * 消息队列弄丢数据
        * 配合confirm机制,消息持久化之后,再给producer发送ACK,若producer未收到ACK,生产者重发
        * 持久化步骤
              * 将queue持久化标识`durable=true`,代表持久化队列
              * 发送消息时将deliveryMode=2
  * 消费者弄丢数据
        * 原因
              * 一般采用自动确认消息模式,消费者自动确认收到的消息.此时RabbitMQ立即将消息删除,若消费者异常则会丢失该消息
      * 解决 
           * 手动确认消息,消费完自己发送确认消息

# 如何保证消息顺序性

* 将消息按顺序放到同一消息队列(Kafka中为partition,RabbitMQ为queue)

* 只用一个消费者消费该队列

  > 保证入队有序,出队以后的顺序交给消费者保证 

# 如何处理消息积压

* 新建topic，新建30个partition
* 将原来消费者消费的消息放入(改消费者代码)该topic中
* 新增30个消费者，消费该partition

# 顺序消息扩容的过程中，如何在不停写的情况下保证消息顺序？

*  成倍扩容,实现扩容前后,同样的key,hash到原队列或者hash到新扩容队列
* 扩容前,记录旧队列的最大位点
* 对于每个Consumer Group,保证旧队列中的数据消费完,再消费新队列(先对新队列禁读)

# 分布式消息队列中,如何对消息重放

* 将Consumer Offset改至需要位置

