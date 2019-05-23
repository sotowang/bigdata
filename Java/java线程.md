# 线程的局部变量和成员变量

[Java 多线程（四） 多线程访问成员变量与局部变量](https://www.cnblogs.com/mengdd/archive/2013/02/16/2913659.html)

**如果一个变量是成员变量**，那么多个线程对**同一个对象**的成员变量进行操作时，它们对该成员变量是彼此影响的，也就是说一个线程对成员变量的改变会影响到另一个线程。

**如果一个变量是局部变量**，那么每个线程都会有一个该局部变量的拷贝（即便是同一个对象中的方法的局部变量，也会对每一个线程有一个拷贝），一个线程对该局部变量的改变不会影响到其他线程。

# 线程池参数

## 线程池的核心的两个队列

* 线程等待池，即线程队列BlockingQueue
* 任务处理池（PoolWorker），即正在工作的Thread列表（HashSet<Worker）

## 线程池的核心参数

* 核心池大小（corePoolSize）

  默认情况下，在创建了线程池后，线程池中的线程数为0，当有任务来之后，就会创建一个线程去执行任务，当线程池中的线程数目达到corePoolSize后，就会把到达的任务放到**缓存队列**当中。**核心线程在allowCoreThreadTimeout被设置为true时会超时退出，默认情况下不会退出**。

* 最大处理线程池数（maxmimumPoolSize）

  当线程数大于或等于核心线程，且任务队列已满时**，线程池会创建新的线程，直到线程数量达到maxPoolSize**。如果线程数已等于maxPoolSize，且任务队列已满，则已超出线程池的处理能力，线程池会拒绝处理任务而抛出异常。

* **keepAliveTime**

  当线程空闲时间达到keepAliveTime，该线程会退出，直到线程数量等于corePoolSize。如果allowCoreThreadTimeout设置为true，则所有线程均会退出直到线程数量为0。

* **allowCoreThreadTimeout**

  是否允许核心线程空闲退出，默认值为false。

* **queueCapacity**



# [为什么用线程池](https://liuzho.github.io/2017/04/17/%E7%BA%BF%E7%A8%8B%E6%B1%A0%EF%BC%8C%E8%BF%99%E4%B8%80%E7%AF%87%E6%88%96%E8%AE%B8%E5%B0%B1%E5%A4%9F%E4%BA%86/)

在Java中，**线程池的概念是Executor这个接口**，具体实现为ThreadPoolExecutor类，对线程池的配置，就是对ThreadPoolExecutor构造函数的参数的配置

# 常见四种线程池

如果你不想自己写一个线程池，那么你可以从下面看看有没有符合你要求的(一般都够用了)，如果有，那么很好你直接用就行了，如果没有，那你就老老实实自己去写一个吧

## CachedThreadPool()

可缓存线程池：

1. 线程数无限制
2. 有空闲线程则复用空闲线程，若无空闲线程则新建线程
3. 一定程序减少频繁创建/销毁线程，减少系统开销

创建方法：

`ExecutorService cachedThreadPool = Executors.newCachedThreadPool();`

## FixedThreadPool()

定长线程池：

1. 可控制线程最大并发数（同时执行的线程数）
2. 超出的线程会在队列中等待

创建方法：

```java
//nThreads => 最大线程数即maximumPoolSize
ExecutorService fixedThreadPool = Executors.newFixedThreadPool(int nThreads);

//threadFactory => 创建线程的方法，这就是我叫你别理他的那个星期六！你还看！
ExecutorService fixedThreadPool = Executors.newFixedThreadPool(int nThreads, ThreadFactory threadFactory);
```

## ScheduledThreadPool()

定长线程池：

1. 支持定时及周期性任务执行。

创建方法：

```java
//nThreads => 最大线程数即maximumPoolSize
ExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(int corePoolSize);
```

## SingleThreadExecutor()

单线程化的线程池：

1. 有且仅有一个工作线程执行任务
2. 所有任务按照指定顺序执行，即遵循队列的入队出队规则

创建方法：

`ExecutorService singleThreadPool = Executors.newSingleThreadPool();`

还有一个`Executors.newSingleThreadScheduledExecutor()`结合了3和4，就不介绍了，基本不用。



# **创建多线程的方式有哪几种？**

方式一：继承Thread类

方式二：实现Runnable接口

方式三：实现Callable接口

方式四：使用线程池的方式

## **解决线程安全问题有几种方式？**

方式一：使用同步代码块

方式二：使用同步方法

方式三：使用ReentrantLock

# Java线程池

[Java线程池实例解析](https://blog.csdn.net/qq_33142257/article/details/75968791)

## 线程池概念

**创建一个线程集合，池子的大小取决于内存数量；当需要执行一个任务的时候是重用一个等待的线程，而不是重新开启一个线程；当任务执行完成后继续回到池子中等待下一个任务**

- 优点：

1、减少在创建和销毁线程上所花的时间和资源开销
2、与主线程隔离，实现异步执行

注意：**池中的线程数不是越多越好，线程休眠同样也会占用资源，所以要合理的选择线程池大小**

- 1、Executors.newCachedThreadPool()；**可缓存的线程池**，当线程越多线程池规模也随之扩大,默认超时时间是60s，如果超过会自动终止该线程

  理论上可开启的线程数是无限的，但是受cpu的影响，**cpu越多的话开的越多**；

- 2、Executors.newFixedThreadPool(2)；创建**一个固定大小的线程池**；**当线程达到最大数时，大小不再变**化，适用于固定的稳定的并发编程

- 3、Executors.newSingleThreadExecutor()；创**建一个单线程**，串行执行

- 4、Executors.newScheduledThreadPool(5)；**计划类线程池**

## 多线程回调

submit：将线程放入线程池中，除了使用execute，还可以使用submit,而且能够获取回调，submit适用于生产者-消费者模式，和Future一起使用起到没有返回结果就阻塞当前线程，等待线程池返回结果；

调用结束打印的语句都是在run方法中返回出来的，**通过future来接受返回参数，然后将future打印**；

通过上面一个例子可以知道Future是阻塞取出结果的，按顺序执行，如果说正常的前面的线程执行完成后面的线程还在执行中的话，前面线程的结果时可以直接返回的，但是如果后面的线程比前面的线程先执行完成，则后面线程的返回结果需要等待前面线程返回后才能取得结果； 而CompletionService是异步非阻塞的，哪个执行完成有回调了，哪个就能输出结果；