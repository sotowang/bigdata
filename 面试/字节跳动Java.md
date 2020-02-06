[TOC]



# 一

**作者：索罗娃·多卡纳 链接：<https://www.nowcoder.com/discuss/203984> 来源：牛客网**

## 一面  

### cookie和session的区别  

* cookie 在客户端
* session在服务端
* 通过cookie实现session

### session的生命周期  

* session调用 `inValidate`
* session到期,`maxAge=-1`

### 有n个服务器，怎样去协调他们使用Redis

分布式锁

`set key value px ... nx `

### Redis 持久化方式  

* RDB
* AOF
* RDB-AOF(官方推荐)

### MySQL索引结构  

* B树
* B+树
* Hash

### MySQL查询优化  

### 最左匹配原则

例子  有ABCD，建立了index(A,C,D)  

查A = xx and D = xx可以吗  

查C = xx and D = xx可以吗  

查C = xx and A = xx可以吗

### 算法

给定数组`nums=[1,2,3,2,3,5,4]`,和一个常数K，找到有没有可能将`nums`分成k个子集，使得每个子集的和相等 ` (5),(1,4),(2,3),(2,3) `

## 二面

https://www.nowcoder.com/discuss/210068

### HTTPS和HTTP的区别  

* SSL加密,更安全
* 端口80与443
* https需要到ca申请证书,一般收费
* HTTP为明文传输

### Java GC分为哪几种  

* Minor GC
* Full GC

### TCP/UDP区别  

* 面向连接VS无连接
* 大量数据VS少量数据
* 可靠(三次握手,段标号)VS不可靠
* 慢VS快

### TCP连接建立的过程  

* 三次握手

### 要是服务器发送的最后一个报文时，客户端挂了会怎样  

A B C D 建立了A，C，D三个索引  ,A = x and C = x 用到了几个索引  

* 一次查询只能用到一个索引

### 算法题

求最长不重复子序列  

## 三面

### 堆排序实现

### Inoodb与MyISAM的区别

* 行级锁与表级锁
* 支持事务
* 索引

### TCP的TIMED_WAITING  

* 保证最后一个ACK报文到达B
* 防止失效的报文连接请求

### 如何写一个多线程  

* 继承Thread(实现了Runnable接口)
* 实现Runanable(推荐)
* 实现Callable
* 线程池

### 线程池有哪几个组件  

* 线程池管理器
* 工作线程
* 任务接口
* 任务队列



---

# 二

https://www.nowcoder.com/discuss/167644 

## 一面 

### 算法

* 找出数组中只出现一次的那个数，其他都出现两次；(异或) 

* 接上面，如果数组中有两个数只出现一次呢？（懵...纸上划了划，还好几分钟后突然灵光一现） 

### 悲观锁和乐观锁 

* 悲观锁
  * 适用于多写,如数据库 行锁,表锁,写锁等
  * ReentrantLock,Synchronized锁
* 乐观锁
  * 适用于多读
  * CAS算法
    * ABA问题(AtomicStampedReference类解决)
    * 长时间循环造成性能开销大
    * 只适用于单个共享变量(JDK1.5之后的AtomicReference解决)
  * 版本号机制
    * 通过对比Version  确定是否更新

### 讲讲你知道的锁

* 悲观锁
* 乐观锁
* 公平锁
* 非公平锁
* 可重入锁
* 分段锁

### ReentrantLock实现原理

* 实现前提为AQS
  * CountDownLatch,FurureTask,Semaphore,Renentrantlock是该抽象类的子类
  * 围绕state变量提供获取和释放
  * tryAcquire获取锁
    * state=0.表示锁无人占有
    * 再判断队列前是否有线程在等待锁,.没有的话调用compareAndSetState使用CAS修改state,传入的acquires写死是1
    * 获取锁成功后,将线程记录为独占锁的线程
    * 线程可以不停lock增加state值(可重入),对应地也要unlock
  * 锁获取失败后加入等待队列(addWaiter),队列节点为Node对象,维护前后Node和指针和等待状态
  * 释放锁
    * 头节点是获取锁的线程,先移除头节点,再通知后面的节点获取锁(sync.release(1))

### 讲讲AQS怎么实现的Fair和NoFair； 

* FairSync
  * 重写tryAcquire方法,参数为1
  * hasQueuePredecessors()保证公平性
  * 只有在队列为空或者当前线程已获得锁(重入)的情况下,才能获得锁
* NonfairSync
  * 若state==0,获得锁
  * 当前线程为拥有锁的线程,获得锁

###  ssh是什么原理？ 

* client向Server发起ssh连接请求
* server向client发起版本协商
  * 比较版本号，看能否工作
* client与server算法协商
  * 压缩算法
  * 加密算法
  * 公钥算法
* 认证阶段
  * 密码认证
    * 客户端公钥加密密码发送至服务端，服务端私钥解密
  * 公钥认证（免密码）
    * 客户端公钥手动复制到Server的authorized_keys文件，相当于Server获取了客户端公钥
    * Server用Client公钥加密256位随机字符串，客户端接收后私钥解密，将字符串与会话id合并，对其用MD5散列函数并把散列值返回给服务器。服务器匹配成功，允许登陆
* 交互阶段

### RSA非对称加密，生成一个公钥和一个私钥

* 公钥加密
* 私钥解密

### Redis的哪些特性 

* 速度快，数据存在内存中，可持久化
  * AOF
  * RDB
* 支持数据类型丰富
  * String，Hash，List，Set，SortedSet
* 操作原子性
* 数据库，缓存，消息中间件

### Redis的单进程单线程模型 

* CPU不是Redis瓶颈，内存，网络带宽才是
* 单线程无法充分利用CPU，可启用多个实例，充分利用CPU
* 单线程可减少CPU切换的开销
* 多线程涉及到锁
* 基于IO多路利用技术

### 延伸到IO多路复用 

* 利用select ,epoll,poll监察多个流的IO事件

* 空闲时将线程阻塞，有IO事件时被唤醒，程序轮询所有的流，（epoll轮询真正有事件的流），依次处理就绪的流，避免无必要操作

* 多路指多个网络连接（流），利用指使用同一进程


### epoll、poll、select

* select 
  * 优势是跨平台,select通过监视文件描述符,调用后select函数阻塞,直到fd就绪或超时,函数返回
  * 函数返回后.遍历fdset,找到就绪的fd
  * 本质是通过就绪或检查fd标志位的数据结构来进行下一步处理
  * 单进程能监视的fd有最大数量限制
  * 采用轮询方法,不管socket是否活跃,都要扫描,CPU时间浪费
  * 需要维护一个存放大量fd的数据结构,用户态与内核态之间的复制开销大
* poll
  * 与select无本质区别,poll使用链表,无最大连接数限制
  * 水平触发(LT),检测到fd事件发生并通知给应用程序,程序未处理,则下次poll会再次报告
  * select与poll都需要在返回后通过遍历fd来获取已经就绪的socket

  

### 再次手撕代码，BST中任意两个节点差的最小值；（题应该算简单的，一紧张脑子就不好用了，磨蹭了会儿才搞出来）； 

### 序列化； 

### 看到简历上有写zookeeper，zookeeper怎么保证数据一致性的； 

## 二面 

### Redis支持哪些数据类型

* String
* List
* Hash
* Set
* ZSet

### Redis中的字符串类型是怎么实现的

* 通过SDS封装了C中的Char数组
* SDS中有free，len，以及char[] ,char[]用来存储字符
* SDS相比原生的char[]有什么优点
  * 获取字符串长度为O(1)
  * 不会造成缓冲区溢出
  * 减少字符串改变造成的空间分配次数
  * 相比原生api更加安全。将分配内存的事进行封装

### 关于Redis对象系统的一个问题

### cookie、session、token三者区别以及优缺点

* cookie在客户端，session在服务端（Tomcat默认20min失效）
* session可通过cookie实现（JSESSIONID设置age为-1）
* session可通过URL回写实现（第一次客户端请求）
* session更加安全
* token会增加服务器额外开销，因为每个访问都要token验证
* token可以防止CSRF攻击，用于单点登陆验证，可以支持跨域访问

### 一致性hash

* 将空间划分出2^32个哈希环
* 将服务器哈希，分散在哈希槽中
* 将数据key哈希，将其值落在哈希槽中，从该位置顺时针出发，落在遇到的第一个node上
* 缺点
  * 在node数少的时候，会造成数据倾斜
* 解决
  * 引入虚拟节点，对node进行多个哈希，都落在哈希槽上
  * 将数据key同样哈希算法哈希，找到node
  * 进行node的映射，将虚节点映射到实际节点上

### git rebase实现原理

rebase会改变server端合并历史，尽量不要使用

### 判断二叉树是否中心对称

### 大数相乘

## 三面 

### 堆排序

项目用到了netty，问了netty相关的一个问题，具体是什么忘了。。 

1. 然后又是select、epoll

2. 项目用到了protostuff序列化库，很多面试官都以为是Google的protobuf，然后就讲讲它们的区别 :) 

3. 项目用到了zookeeper，讲讲你对zk的理解吧；（主要讲了zk的数据模型和watch机制，zab记得不牢不敢讲） 

---

# 三

https://www.nowcoder.com/discuss/363312?type=0&order=0&pos=59&page=1

## 一面

### 讲讲项目

### 在浏览器的URL栏里输入一个HTTPS的请求会发生什么？

* DNS域名解析
* 建立TCP连接（3次握手）
* 发送HTTP请求
* 服务器返回响应体
* 浏览器接收响应内容并渲染
* 关闭连接

### 三次握手

* 主机A向B发送SYN=1，Seq=x  **状态：SYN_SEND**
* B看到SYN=1，知道此时有人要建立连接，于是向A发送ack=x+1,ACK=1,seq=y,SYN=1  **状态：SYN_RECV**
* A检查ACK=1且ack=x+1都正确，此时向B发送ack=y+1,ACK=1,建立连接  **状态：ESTABLISHED**

### 四次挥手

* A发送FIN报文给B（fin-wait-1）
* B发送ACK报文给A (CLOSE_WAIT)
* B发送FIN报文给A
* A发送ACK报文给B(time_wait)

### CLOSE_WAIT ，TIME_WAIT原理

* CLOSE_WAIT
  * 被动关闭连接时，当Client已收到FIN报文后，未发送自己的FIN报文给Server，此时处于CLOSE_WAIT
  * 该状态时Client因在做一些其它的事情，如读或写文件未能发送FIN报文
  * 该状态默认至少有2个小时

* TIME_WAIT
  * 发起关闭连接方会在最后发送ACK时处于该状态
  * 作用
    * 若最后ACK未能在2MSL内发送到Client，Client会触发超时重传
    * 让旧连接能够及时关闭，杜绝在新连接中出现旧连接
      * 如在linux中关闭一个服务器后，再打开会发现端口占用

### 内存泄露原因，举例子，怎么排查，OOM异常

### 静态内部类会被编译成几个class？为什么内部类可以访问外部类的private的方法？

### 多线程安全，线程池相关问题

### 集合框架源码，ConcurrentHashmap，HashMap,LinkedHashMap实现LRU

### 为什么需要锁，你知道几种锁,说说AQS,说说CAS

### synchronized原理，jdk6对他的优化，（偏向锁等），JNI，逃逸分析，对象的mark word

### 知道几个设计模式，怎么用，手写双重检查锁单例模式

### 以下代码题输出什么？（巨坑，输出100，从泛型+向上转型+map+equals原理上想）