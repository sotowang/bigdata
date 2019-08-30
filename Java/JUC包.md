# JUC包分类

* 线程执行器executor
* 锁locks
* 原子变量类atomic
* 并发工具类tools
  * 闭锁 CountDownLatch
  * 栅栏 CyclicBarrier
  * 信号量 Semaphore
  * 交换器 Exchanger

* 并发集合collections
  * BlockingQueue
    * ArrayBlockingQueue
      * 一个由数组结构组成的有界阻塞队列
    * LinkedBlockingQueue
      * 一个由链表结构组成的有界/无界阻塞队列
    * PriorityBlockingQueue
      * 一个支持优先级排序的无界阻塞队列
    * DelayQueue
      * 一个使用优先级队列组成的无界阻塞队列
    * SynchronousQueue
      * 一个不存储元素的阻塞队列
    * LinkedTransferQueue
      * 一个由链表结构级成的无界阻塞队列
    * LinkedBlockingDeque
      * 一个由链表结构组成的双向阻塞队列





# 并发工具类

`CountDownLatch` 是能使一组线程等另一组线程都跑完了再继续跑

CountDownLatch的最简单使用，CountDownLatch同步计数器从5开始计数，分别对应5个子线程的业务完成情况。每当一个子线程业务完成后，CountDownLatch同步计数器就countDown一次。直到count等于0时，这时主线程上面的await()方法解除等待/阻塞状态，继续执行。这里要注意一下：

- 不是说只能有一次await方法的调用，而是同一时间可以有多个线程调用了await方法。只要在count还不等于0时，某个线程调用了await方法，它都会进入等待/阻塞状态。
- 在调用await时，如果CountDownLatch同步计数器的count已经等于0了，则await方法不会进入等待/阻塞状态。
- await调用和countDown调用不是说必须处于不同线程。同一线程中，您可以先调用countDown然后再调用await进入等待/阻塞。CountDownLatch同步计数器会始终遵循上两条工作原则。
- 在使用CountDownLatch同步计数器时，您无需考虑脏数据的问题。CountDownLatch同步计数器是线程安全的。

 

` CyclicBarrier` 能够使一组线程在一个时间点上达到同步，可以是一起开始执行全部任务或者一部分任务。同时，它是可以循环使用的。

* 总结

CountDownLatch和CyclicBarrier都能够实现线程之间的等待，只不过它们侧重点不同：

CountDownLatch一般用于某个线程A等待若干个其他线程执行完任务之后，它才执行；

而CyclicBarrier一般用于一组线程互相等待至某个状态，然后这一组线程再同时执行；

另外，CountDownLatch是不能够重用的，而CyclicBarrier是可以重用的。
