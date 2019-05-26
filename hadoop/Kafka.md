# **请说明Kafka相对传统技术有什么优势?**

　　快速:单一的Kafka代理可以处理成千上万的客户端，每秒处理数兆字节的读写操作。

　　可伸缩:在一组机器上对数据进行分区和简化，以支持更大的数据

　　持久:消息是持久性的，并在集群中进行复制，以防止数据丢失。

　　设计:它提供了容错保证和持久性



Topic：特指Kafka处理的消息源（feeds of messages）的不同分类。

Partition：Topic物理上的分组，一个topic可以分为多个partition，每个partition是一个有序的队列。partition中的每条消息都会被分配一个有序的id（offset）。

Message：消息，是通信的基本单位，每个producer可以向一个topic（主题）发布一些消息。

Producers：消息和数据生产者，向Kafka的一个topic发布消息的过程叫做producers。

Consumers：消息和数据消费者，订阅topics并处理其发布的消息的过程叫做consumers。

Broker：缓存代理，Kafka集群中的一台或多台服务器统称为broker。

2、负载均衡原理

producer根据用户指定的算法，将消息发送到指定的partition

存在多个partiiton，每个partition有自己的replica，每个replica分布在不同的Broker节点上

多个partition需要选取出lead partition，lead partition负责读写，并由zookeeper负责fail over

通过zookeeper管理broker与consumer的动态加入与离开



#  Kafka主要特点？

1 同时为发布和订阅提供高吞吐量。据了解，Kafka每秒可以生产约25万消息（50 MB），每秒处理55万消息（110 MB）。
 2 可进行持久化操作。将消息持久化到磁盘，因此可用于批量消费，例如ETL，以及实时应用程序。通过将数据持久化到硬盘以及replication防止数据丢失。
 3 分布式系统，易于向外扩展。所有的producer、broker和consumer都会有多个，均为分布式的。无需停机即可扩展机器。
 4 消息被处理的状态是在consumer端维护，而不是由server端维护。当失败时能自动平衡。
 5 支持online和offline的场景。

 

# Kayka的应用场景

1、消息队列
2、行为跟踪：跟踪用户浏览页面、搜索及其他行为，以发布-订阅的模式实时记录到对应的topic里
3、元信息监控：运维性质的数据监控
4、日志收集
5、流处理
6、事件源
7、持久性日志（commit log） 

 

 

 