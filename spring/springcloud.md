# 服务治理 Eureka

提供服务注册和服务发现功能

* 服务治理核心
  * 服务提供者
  * 服务消费者
  * 注册中心 
* Eureka Server  
  * 注册中心
* Euraka Client
  * 所有要进行注册的微服务通过Euraka Client 连接到Euraka Server，完成注册

## RestTemplate

* 什么是RestTemplate？

  RestTemplate是Spring 框架提供基于REST的服务组件，底层是对HTTP请求及响应进行了封装，提供了很多访问REST服务的方法，可以简化代码开发。

* 如何使用RestTemplate？

  



# 负载均衡 Ribbon

* 什么是Ribbon?
  * 基于Netflix Ribbon实现的,是一个用于对HTTP请求进行控制的负载均衡客户端
  * Ribbon基于负载均衡算法自动帮助服务消费者调用接口
    * 轮询
    * 随机
    * 加权轮询
    * 加权随机
    * 自定义
* @LoadBalanced
  * 声明一个基于Ribbon的负载均衡服务

# 服务通信 Feign

# 服务网关 Zuul

*  什么是Zuul
     * Netflix提供的开源API网关服务器
     *  客户端和网站所有请求的中间层,对外开放一个API
     *  将所有请求导入统一的入口,屏蔽了服务端的具体实现逻辑
     *  Zuul可以实现反向代理的功能,在网关内部实现动态路由,身份谁,IP过滤,数据监控
*  Zuul自带了负载均衡功能,修改provider的代码

  

# 服务容错 Hystrix

# 服务配置 Config

# 服务监控 Actuator

# 服务跟踪 Zipkin