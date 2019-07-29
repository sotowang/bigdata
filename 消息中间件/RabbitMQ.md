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

* Publisher

* Exchange

  * direct
  * fanout
  * topic
  * headers

* Queue

* Binding

* Connection

* Channel

  