# Java内存模型(JMM)

## 缓存行 cache line

* 对齐

## 原子性

## 可见性

* Java中可见性措施
  * 被final修饰的字段初始化完成能确保可见性
  * volatile
  * synchronized
  * 符合happens-befor次序法则的可见性

## 有序性

* 概念
  * 程序执行的jdyc按照代码的顺序执行
* Java中有序性的措施
  * volatile 
  * synchronized
  * Happens-before法则

## 代码重排

* 为了使处理器内部的去运算单元充分利用
* 编译期重排
  * 编译器依据上下文分析,对指令进行重排序,以更适合cpu运行
* 运行期重排

## 内存屏障

* 阻止屏障两边的指令重排

### volatile

* 轻量同步,不会进行线程阻塞
* 读写直接作用于主内存
* 保证变量可见性,不保证原子性

# JUC框架

## 锁

### 乐观锁CAS(compare and swap)

* ABA问题
  * 通过版本号机制解决

### 悲观锁

* synchronized
  * 对象锁
  * 类锁
  * 非公平锁
    * 基于JVM实现
      * 偏向锁(CAS)
      * 轻量级锁
      * 重量级锁
  * wait/notify
    * notify唤醒锁之后需要等该代码块执行完才会释放锁
* reentryLock
    * 基于代码层面实现
    * Unlock()操作必须放到finally块中
    * 底层基于AQS的独占锁实现
    * 可重入,通过AQS的同步状态来保存锁重入次数
  * AQS
    * 线程管理队列-->双向node
    * 尾部CAS入队
    * 头部释放锁出队的时候唤醒next节点
* Condition
    * 用于线程间的协作
* 读写锁ReadWriteLock
    * 基于AQS
        * 高16位读锁次数
        * 低16位写锁次数
* 闭锁CountdownLatch
    * 基于AQS,一次性的
    * 一个线程等待别一个线程结束(countDown(),await())

* 栅栏CyclicBarrier

  * 基于condition-await/SignalAll
  * 一种计数方式,可重用
  * 等待n个线程一起开始业务

* 信号量

  * 基于AQS共享锁的实现
  * 同步状态state表示当前计数
  * 当计数<=0时,阻塞;大于0时解除阻塞

* 分布式锁

  * > 找到一个单点,锁状态持久化

  * 基于数据库

  * 基于redis

    * RedLock算法
      * 在N个节点下,获取到>=N/2+1,就算获取锁

  * 基于zookeeper

    * QPS高时不要使用

# 并发容器

* ConcurrentHashMap
* CopyOnWriteArrayList
  * 写时复制,引发young gc
* 阻塞队列
  * 基于数组结构实现的有界队列

# 线程池

* 拒绝策略

# 协程

* 轻量级线程
  * 创建和切换开销小
  * 协程高度在用户态完成

# 缓存失效解决

* 双写
* 缓存失效后，单机只用其中一个请求线程去db捞取
* 缓存失效前，单机去db捞取