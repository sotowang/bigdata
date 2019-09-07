# 为什么用线程池

* 线程池的底层为`ThreadPoolExcutor`类，对线程池的配置实际是对该类构造函数的配置

- 降低资源消耗，重复利用已建线程降低创建销毁的消耗
- 提高响应速度。任务到达时，可以不需要等到线程创建就立即执行
- 提高线程的可管理性

# Fork/Join框架

* 把大任务分割成若干小任务并行，最终汇总每个小任务结果后得到大任务结果的框架。

* Work-Stealing算法

  * 某个线程从其他队列里窃取任务来执行
  * 窃取的任务需是最晚的任务,防止竞争



# 线程的局部变量和成员变量

[Java 多线程（四） 多线程访问成员变量与局部变量](https://www.cnblogs.com/mengdd/archive/2013/02/16/2913659.html)

**如果一个变量是成员变量**，那么多个线程对**同一个对象**的成员变量进行操作时，它们对该成员变量是彼此影响的，也就是说一个线程对成员变量的改变会影响到另一个线程。

**如果一个变量是局部变量**，那么每个线程都会有一个该局部变量的拷贝（即便是同一个对象中的方法的局部变量，也会对每一个线程有一个拷贝），一个线程对该局部变量的改变不会影响到其他线程。

# 线程池参数(7个)

* corePoolSize
  * 线程数目达到corePoolSize，会把到达的任务放到**缓存队列**当中
  * 核心线程在allowCoreThreadTimeout被设置为true时会超时退出，默认情况下不会退出。
* maximumPoolSize
  * 线程不够用时能够创建的最大线程数
* keepAliveTime

  * 当线程空闲时间达到keepAliveTime，该线程会退出，直到线程数量等于corePoolSize。如果allowCoreThreadTimeout设置为true，则所有线程均会退出直到线程数量为0。
* unit
  * keepAliveTime的单位
* workQueue
  * 任务队列，被提交但尚未被执行的任务
* threadFactory
  * 生成线程池中工作线程的线程工厂，用于创建线程一般默认的即可
* handler
  * 拒绝策略，当队列满了且工作线程>=maximumPoolSize


# ThreadPoolExecutor执行的策略

>  新建线程 -> 达到核心数 -> 加入队列 -> 新建线程（非核心） -> 达到最大数 -> 触发拒绝策略

* execute() 无返回值,无法任务是否成功

  * 线程数量未达到corePoolSize，则新建一个线程(核心线程)执行任务
  * 线程数量达到了corePools，则将任务移入队列等待
  * 队列已满，新建线程(非核心线程)执行任务
  * 队列已满，总线程数又达到了maximumPoolSize，就会由(RejectedExecutionHandler)抛出异常
* submit() 有返回值futire

  * future.get() 获得返回值
* 拒绝策略
  * AbortPolicy	(默认)
    * 不执行新任务,直接抛出异常,提示线程已满
  * DiscardPolicy (静默)
    * 不执行新任务,也不抛出异常
  * DiscardOldSetPolicy
    * 将消息队列的第一个替换为当前新来的任务执行
  * CallerRunPolicy
    * 用于被拒绝任务的处理程序,直接在execute()方法的调用线程中运行被拒绝的任务
    * 如果执行程序已关闭,则会丢弃该任务 


# 线程池状态

* RUNNING 
  * 能接受新提交的任务，并且也能处理阻塞队列中的任务
* SHUTDOWN
  * 不再接受新提交的任务。但可以处理存量任务
* STOP
  * 不再接受新提交的任务，也不处理存量任务
* TIDYING
  * 所有的任务都已终止
* TERMINATED
  * terminated() 方法执行完后进入该状态

![深度截图_选择区域_20190605130036.png](https://i.loli.net/2019/06/05/5cf74c87a2e2989507.png)

# 线程池关闭

* shutdown()
  * 只将线程池的状态高成SHUTDOWN状态,然后中断没有正在执行任务的线程
* shutdownNow()
  * 遍历线程池中工作线程,逐个调用线程的interrupt方法中断
  * 无法响应中断的任务可能永远无法终止
  * shutdownNow会首先将线程池的状态设置成STOP，然后尝试停止所有的正在执行或暂停任务的线程，并返回等待执行任务的列表。

# 线程池大小配置

* CPU密集型

  * `线程数=按照核数或者核数+1`设定
* I/O密集型
  * `线程数=CPU核数*(1+平均等待时间/平均工作时间)`

# 线程池主要组件

* 线程池管理器(ThreadPool)
  * 创建线程池
  * 销毁线程池
  * 添加任务
* 工作线程(WorkThread)
  * 在没有任务时处于等待状态,可以循环执行任务
* 任务接口(Task)
  * 每个任务必须实现的接口,以供工作线程调试任务的执行,规定了任务的入口,任务执行完后收尾工作,任务执行状态
* 任务队列(taskQueue)
  * 存放没有处理的任务,提供一种缓冲机制

# 常见四种线程池

* `CachedThreadPool() `  可缓存线程池

  * 线程数无限制

  * 有空闲线程则复用空闲线程，若无空闲线程则新建线程

  * 一定程序减少频繁创建/销毁线程，减少系统开销

    ```java
    ExecutorService cachedThreadPool = Executors.newCachedThreadPool();
    ```

* `FixedThreadPool()`   定长线程池

  1. 可控制线程最大并发数（同时执行的线程数）
  * 超出的线程会在队列中等待

    ```java
    //nThreads => 最大线程数即maximumPoolSize
    ExecutorService fixedThreadPool = Executors.newFixedThreadPool(int nThreads);
    ```

* `ScheduledThreadPool()`  定时线程池

  * 支持定时及周期性任务执行。

    ```java
    //nThreads => 最大线程数即maximumPoolSize
    ExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(int corePoolSize);
    ```

    

* `SingleThreadPool() ` 单线程化的线程池

  * 有且仅有一个工作线程执行任务

  * 所有任务按照指定顺序执行，即遵循队列的入队出队规则

    ```java
    ExecutorService singleThreadPool = Executors.newSingleThreadPool();
    ```



# 解决线程安全问题方式

*　使用同步代码块

* 使用同步方法
* 使用ReentrantLock

# 缓冲队列BlockingQueue

* ArrayBlockingQueue(int i)
  * 必须指定大小
  * 所含对象为FIFO顺序
* LinkedBlockingQueue() 或者int(i)
  * 大小不固定,若构造时指定大小,则有大小限制;不指定大小,则为Integer.MAX_VALUE
  * 所含对象为FIFO顺序
* PriorityBlockingQueue 或者(int i)
  * 所含对象的排序不是FIFO,而是依据对象的自然顺序或者构造函数的Comparator决定
* SynchronizedQueue()
  * 特殊的BlockingQueue,对其操作必须是放和取代交替完成





