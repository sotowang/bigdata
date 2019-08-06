# 消息服务中间件

*  大多应用中可通过消息服务中间件来提升系统异步通信,扩展解耦能力
* 消息服务中两个重要概念
  * 消息代理(message broked)
  * 目的地(destination)
* 点对点式
  * 消息发送者发送消息,消息代理将其放入一个队列中,消息接收者从队列中获取消息内容,消息读取后被移出队列
  * 消息只有唯一的发送者和接受者,但并不是说只有一个接收者
* 发布订阅式
  * 发送者发送消息到主题,多个接收者(订阅者)监听(订阅为个主题),在消息到达时同时收到消息
* JMS(Java Message Service) JAVA消息服务
  * 基于JVM消息代理的规范
  * ActiveMQ,HornetMQ是JMS实现
* AMQP(Advanced Message Queuing Protocol)
  * 高级消息队列协议,消息代理规范
  * RabbitMQ



# RabbitMQ

* Message
  * 由Properties和Body组成
  * Properties对消息修饰(消息优先级,延迟等)
  * Body 消息体内容 
* Publisher
* Exchange

  * direct(单播)
    * 根据路由键将消息派发给指定队列
  * fanout(广播)
    * 每个消息都会分到所有绑定队列上去
    * fanout类型转发消息是最快的
    * 像子网传播,每台子网内的主机都获得了一份复制消息
  * topic(有选择广播)
    * 通过模式匹配分配消息的路由键属性
    * #匹配0个或多个单词,*匹配一个单词,单词之间用点隔开
  * headers
* Queue
  * 保存消息并转发给消费者
* Binding
  * Exchange和Queue之间的虚拟连接
  * Binding中可以包含Routingkey
* Connection
  * 连接,应用程序与Broker连接
* Channel 信道
  * 进行消息读写的通道
  * 客户端可以建立多个Channel
* Consumer
* Virtual Host
  * 虚拟主机,用于逻辑隔离,最上层消息路由
  * 一个Virtual Host里面可以有若干个Exchange和Queue
  * 同一个Virtual Host里面不能有相同名称的Exchange或Queue
* Broker
  * 接受客户端的连接,实现AMQP实体服务



## RabbitMQ 运行

> docker run -d -p 5672:5672 -p 15672:15672 --name myrabbitmq 3f92e6354d11

>  sudo rabbitmq-server start

* 使用插件 

  > rabbitmq-plugins  enable rabbitmq_management 



 自动配置

* RabbitAutoConfiguration
* 有自动配置了连接工厂ConnectionFactory
* RabbitProperties 封装了 RabbitMQ的配置
* RabbitTemplate:给RabbitMQ发送和接收消息
* AmqpAdmin  RabbitMQ系统管理组件

  