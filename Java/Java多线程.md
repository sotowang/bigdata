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
* 使用Thread类的join()阻塞当前前线程以等待子线程处理完毕
* 通过Callable接口实现：通过RutureTask Or线程池获取

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
  * 如果线程处于正常活动状态，那么会将该线程的中断标志位设置为true，被徽墨中断标志的线程将继续正常运行，不受影响。