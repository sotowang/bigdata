# 基本命令

* 启动nameServer

  > nohup ./mqnamesrv &

* 启动broker

  > nohup  sh mqbroker -n 127.0.0.1:9876 -c ../conf/2m-noslave/broker-a.properties > broker.out   &

* broker查看 

  > ./mqadmin clusterList -n 127.0.0.1:9876

* 关闭broker

  > ./mqshutdown broker

* 关闭 nameServ

  > ./mqshutdown namesrv

# 概念模型

* Produccer

  * 失败默认重试2次
  * 在事务消息机制中,若发送消息的producer在还未commit/rollback前挂掉,broker会在一段时间回查ProducerGroup里的其他实例,确认消息应该commit/rollback

* Consumer

  * CLUSTERING模式下,一条消息只会被ConsumerGroup里的一个实例消费,但可被多个不同ConsumerGroup消费
  * BROADCASTING模式下,一条消息会被ConsumerGroup里的所有实例消费

* Push Consumer
  * Consumer的一种,需要向Consumer对象注册监听
  * 使用pull实现,采用**长轮询**
    * Broker挂起客户端请求一段时间,若有新消息到达,立刻返回Consumer
    * 主动权在Consumer中,即使Broker有大量消息积压,也不会主动推送给Consumer
    * 挂起请求时需要占用资源,适合消息队列这种客户端可控的场景
  * Broker收到消息请求
    * 若队列里无新消息,通过循环不断查看状态,每次waitForRunning5s,再check.每3次check时,等待时间超过15s(suspendMaxTimeMills),返回空.
    * 若收到消息,通过notifyMessageArriving返回请求结果

* Pull Consumer
  * Consumer的一种,需要用户主动请求Broker 拉取消息

* Producer Group
  * 生产者集合,一般用于发送一类消息

* Consumer Group
  * 消费者集合,一般用于接受一类消息进行消费

    

* Broker
  * MQ消息服务(中转角色,用于消息存储与生产消费转发)
  * Broker存Topic信息,Topic,由多个队列组成,队列平均分散在多个Broker上
  * Producer发送机制保证消息尽量平均分配到所队列中

* NameServer
  * 可以部署多个,相互之间独立
  * 其他角色向多个NameServer上报状态信息,从而达到热备份目的
  * 本身无状态,Broker,Topic等状态信息不会持久存储,由各角色上报存在内存

* Topic

  * 默认4个队列,需要顺序消费的消息送同一队列

* queueNums(队列数)

  * 客户端自动创建,最多创建8个队列,若要超过8个,可通过控制台创建/修改,Topic配置保存在store/config/topics.json
  * 消费负载均衡小粒度为队列,Consumer数量不在于队列数
  * 读写队列数(writeQueueNums/readQueueNums)为RocketMQ特有概念,可通过console修改

# RocketMQ 生产者

## 核心参数

* producerGroup -----组名
* createTopicKey 
* defaultTopicQueueNums (默认为4)
* sendMsgTimeout (单位:ms)
* compressMsgBodyOverHowmuch  (默认压缩字节4096)
* retryTimesWhenSendFailed
* retryAnotherBrokerWhenNotStoreOK(默认false)
* maxMessageSize(默认128k)

## 主从同步机制解析

* Master - Slave主从同步 
* 同步信息
  * 数据内容
  * 元数据信息
* 元数据同步
  * Broker角色识别,为Slave启动同步任务
* 消息同步
  * HAService
  * HAconnection
  * WaitNotifyObject

## 同步消息发送

* producer.send(Message msg)

## 异步消息发送

* producer.send(Message msg,SendCallback sendCallback)
* 异步发送消息核心实现
  * DefaultMQProducerImpl

# 包结构 

* rocketmq-broker
  * 主要的业务逻辑,消息收发,主从同步,pagecache
* rocketmq-client 
  * 客户端接口,比如生产者和消费者
* rocketmq-example
  * 示例
* rocketmq-common
  * 公用数据结构
* rocketmq-distribution
  * 编译模块,编译输出等
* rocketmq-filter
  * 进行Broker过滤的不感兴趣的消息传输,减小带宽压力
* rocketmq-logappender    rocketmq-logging
  * 日志相关
* rocketmq-namesrv
  * Namesrv服务,用于服务协调
* rocketmq-openmessaging
  * 对外提供服务
* rocketmq-remoting
  * 远程调用接口,封装Netty底层通信
* rocketmq-srvutil
  * 公用的工具方法,比如解析命令行参数
* rocketmq-store
  * 消息存储
* rocketmq-tools
  * 管理工具(mqadmin)

# 集群环境

* 单点模式
* 主从模式
  * 保障消息即时性与可靠性
* 双主模式
* 双主双从模式  多主多从模式 

# 心跳机制

* 单个Broker跟所有Namesrv保持心跳请求,间隔30秒,心跳请求包括当前Broker所有Topic

* Namesrv反查Broker心跳信息,若某Broker2分内无心跳,则认为其下线,调整Topic跟Broker对应关系,但Namesrv不会主动通知Producer,Consumer有Broker宕机

* Consumer跟Broker长连接,每30s发心跳.Broker每10s检查当前存活Consumer,若某Consumer2分内无心跳,则断开连接,并向Consumer Group其它实例发送通知,触发消费者集群的负载均衡

* producer每30s从Namesrv获取Topic跟Broker映射关系,更新至本地内存,再与Topic涉及的所Broker建立长连接,每隔30s发一次心跳.Broker端每隔10s检查当前注册的Producer,若2分内无心跳,则断开连接 

# Namesrv开销

  * 维持心跳,提供Topic-Broker的关系数据
  * Broker向Namesrv发心跳时会带上所负责的Topic信息,若Topic太多(万级别),会导致一次心跳中Topic几十M,网络差时可能导致心跳失败

# 消息存储

* 由ConsumeQueue和CommitLog配合完成 

  >  ConsumeQueue只存储少量数据,消息主体通过CommitLog进行读写

  > 若某个消息只在CommitLog中有数据,在ConsumeQueue中没有,则消息无法消费,RocketMQ的事务利用了这一点

  * CommitLog
    * 消息主体以及元数据的存储主体,对CommitLog建立一个ConsumeQueue,每个ConsumeQueue对应一个MessageQueue,所以只要有CommitLog在.ConsumeQueue即使数据丢失,仍然可以恢复
  * ConsumeQueue
    * 消息的逻辑队列,存储Queue在CommitLog 中起始offset,log大小和MessageTag的hashCode,log大小和MessageTag的hashCode.每个Topic下的每个Queue都有一个对应 的ConsumeQueue
  * 