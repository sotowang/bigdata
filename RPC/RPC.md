#  [RPC架构组件](https://juejin.im/post/5cd28d1ee51d453aa44ad6d8)

一个基本的RPC架构里面应该至少包含以下4个组件：

1. **客户端** （Client）:服务调用方（服务消费者）
2. **客户端存根** （Client Stub）:存放服务端地址信息，将客户端的请求参数数据信息打包成网络消息，再通过网络传输发送给服务端
3. **服务端存根** （Server Stub）:接收客户端发送过来的请求消息并进行解包，然后再调用本地服务进行处理
4. **服务端** （Server）:服务的真正提供者

# [RPC使用了哪些关键技术](https://juejin.im/post/5cd28d1ee51d453aa44ad6d8)

**1、动态代理**

* 生成Client Stub（客户端存根）和Server Stub（服务端存根）的时候需要用到Java动态代理技术，可以使用JDK提供的原生的动态代理机制，也可以使用开源的：CGLib代理，Javassist字节码生成技术。

**2、序列化和反序列化**

* 在网络中，所有的数据都将会被转化为字节进行传送，所以为了能够使参数对象在网络中进行传输，需要对这些参数进行序列化和反序列化操作。
* 目前比较高效的开源序列化框架：如Kryo、FastJson和Protobuf等。

**3、NIO通信**

* 出于并发性能的考虑，传统的阻塞式 IO 显然不太合适，因此我们需要异步的 IO，即 NIO。Java 提供了 NIO 的解决方案，Java 7 也提供了更优秀的 NIO.2 支持。可以选择Netty或者MINA来解决NIO数据传输的问题。

**4、服务注册中心**

* 可选：Redis、Zookeeper、Consul 、Etcd。一般使用ZooKeeper提供服务注册与发现功能，解决单点故障以及分布式部署的问题(注册中心)。

# 原理架构图

![image](https://user-gold-cdn.xitu.io/2019/5/8/16a967782dea01f1?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

![image](https://user-gold-cdn.xitu.io/2019/5/8/16a96777705bab4b?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)