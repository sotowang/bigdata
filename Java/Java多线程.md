# Thread中的start和run方法的区别

* 调用start()方法会创建一个新的子线程并启动
* run() 方法只是Thread的一个普通方法的调用

# Thread和Runnable的关系

* Thread是实现了Runnable接口的类，使得run支持多线程
* 因类的单一继承原则，推荐多使用Runnable接口

# 如何给run() 方法传参

* 构造函数传参
* 成员变量传参
* 回调函数传参

# 如何实现处理线程的返回值

* 主线程等待法
* 使用Thread类的join()阻塞当前线程以等待子线程处理完毕
* 通过Callable接口实现：
  * 通过FutureTask 
  * 通过线程池 获取 Callable实例

# 线程的状态

* 新建（new） 
  * 创建后尚未启动的线程状态（未start）
* 运行（Runnable）
  * 包含Running和Ready
* 无限期等待（Waitting）
  * 不会被分配CPU执行时间，需显示唤醒
    * 没有设置Timeout参数 Object.wait()方法
    * 没有设置Timeout参数的Thread.join()方法
    * LockSupport.park()方法
* 限期等待（Time Waiting）
  * 在一定时间后会由系统自动唤醒
    * Thread.sleep()方法
    * 设置了Timeout参数的Object.wai()方法
    * 设置了Timeout参数的Thread.join()方法
    * LockSupport.parkNanos()方法
    * LockSupport.parkUntil()方法
* 阻塞（Blocked）
  * 等待获取排它锁
* 结束（Terminated）
  * 已终止线程的状态，线程已结束执行

# sleep的wait的区别

* sleep是Thread类的方法，wait是Object类中定义的方法
* sleep()方法可以在任何地方使用
* wait()方法只能在synchronized方法或synchronized块中使用

**最本质区别**

* Thread.sleep只会让出CPU，不会导致锁行为的改变
* Object.wait不仅让出CPU，还会释放已占有的同步资源锁

# notify和notifyAll的区别

**锁池EntryList**

**等待池WaitSet**

* notifyAll 会让所有处于等待池的线程全部进入锁池去竞争获取锁的机会
* notify只会随机选取一个处于等待池中的线程进入锁池去竞争获取锁的机会

# yield 函数

当调用Thread.yield()函数时，会给线程调度器一个当前线程愿意让出CPU使用的暗示，但是线程调度很可能忽略这个暗示。

# interrupt函数

* 调用interrupt()，通知线程应该中断了
  * 如果线程处于被阻塞状态，那么线程将立即退出被阻塞状态，并抛出一个InterruptedException异常
  * 如果线程处于正常活动状态，那么会将该线程的中断标志设置为true。被设置中断标志的线程将继续正常运行，不受影响。
* 需要被调用的线程配合中断
  * 在正常运行任务时，经常检查本线程的中断标志位，如果被设置了中断标志就自行停止线程。
  * 如果线程处于正常活动状态，那么会将该线程的中断标志位设置为true，被设置中断标志的线程将继续正常运行，不受影响。

# ThreadLocal

* 类似HashMap的数据结构,可以保存"key:value"键值对,一个ThreadLocal只能保存一个,并且各个线程的数据互不干扰

* 可能导致,内存泄漏(Entry为弱引用,实际不会造成内存泄漏)

* set方法

  * 通过当前线程对象thread获取该thread所维护的threadLocalMap
  * 若threadLocalMap不为null,则以threadLocal实例为key,值为value的键值对存入threadLocalMap
  * 若threadLocalMap为null的话，就新建threadLocalMap然后在以threadLocal为键，值为value的键值对存入即可

* get方法

  * 通过当前线程thread实例获取到它所维护的threadLocalMap，

  * 以当前threadLocal实例为key获取该map中的键值对（Entry）

  * 若Entry不为null则返回Entry的value。

  * 如果获取threadLocalMap为null或者Entry为null的话，就以当前threadLocal为Key，value为null存入map后，并返回null。



# 为何stop()和suspend()方法不推荐使用

* stop（）方法
  * 作为一种粗暴的线程终止行为，在线程终止之前没有对其做任何的清除操作，因此具有固有的不安全性。 用Thread.stop()方法来终止线程将会释放该线程对象已经锁定的所有监视器。如果以前受这些监视器保护的任何对象都处于不连贯状态，那么损坏的对象对其他线程可见，这有可能导致不安全的操作。 由于上述原因，因此不应该使用stop()方法
  * 应该在自己的Thread类中置入一个标志，用于控制目标线程是活动还是停止。如果该标志指示它要停止运行，可使其结束run（）方法。如果目标线程等待很长时间，则应使用interrupt()方法来中断该等待。
* suspend()方法 
  * 它具有固有的死锁倾向。
  * 调用suspend（）方法的时候，目标线程会停下来。如果目标线程挂起时在保护关键系统资源的监视器上保持有锁，则在目标线程重新开始以前，其他线程都不能访问该资源。除非被挂起的线程恢复运行。
  * 对任何其他线程来说，如果想恢复目标线程，同时又试图使用任何一个锁定的资源，就会造成死锁。由于上述原因，因此不应该使用suspend（）方法，
  * 应在自己的thread类中置入一个标志，用于控制线程是活动还是挂起。如果标志指出线程应该挂起，那么用wait（）方法命令其进入等待状态。如果标志指出线程应当恢复，那么用notify()方法重新启动线程。

​     

​     

​     

​     

​     



​     

​     

​     

​     

​     