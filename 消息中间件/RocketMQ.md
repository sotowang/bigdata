# 基本命令

* 启动nameServer

  > nohup ./mqnamesrv &

* 启动broker

  > nohup  sh mqbroker -n 127.0.0.1:9876 -c ../conf/2m-noslave/broker-a.properties > broker.out   &

* broker查看 

  > ./mqadmin clusterList -n 127.0.0.1:9876

# 概念模型

* Produccer
* Consumer
* Push Consumer
  * Consumer的一种,需要向Consumer对象注册监听
* Pull Consumer
  * Consumer的一种,需要主动请求Broker 拉取消息
* Producer Group
  * 生产者集合,一般用于发送一类消息
* Consumer Group
  * 消费者集合,一般用于接受一类消息进行消费
* Broker
  * MQ消息服务(中转角色,用于消息存储与生产消费转发)

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



