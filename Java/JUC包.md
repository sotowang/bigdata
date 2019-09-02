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

## CountDownLatch

* 让一组线程等另一组线程跑完再继续跑
* 每当一子线程完成任务，同步计数器就`countDown()`一次，直到count为0，此时主线程上的`await()`方法解除等待/阻塞状态，继续执行
  * 在调用await时，若同步计数器的count刚好为0，此时不会进入等待/阻塞状态
  * 同一线程也可以先调用countDown再调用`await()`

- CountDownLatch同步计数器是线程安全的,无需考虑脏数据的问题。

  ```java
  CountDownLatch countDownLatch = new CountDownLarch(6);
  //在线程内执行
  countDownLatch.countDown();
  //在主线程下执行
  countDownLarch.await();
  ```

##  CyclicBarrier

*  让一组线程在一个时间点上达到同步，可以是一起开始执行全部/部分任务。

* 与`CountDownLatch`作用相反

* CyclicBarrier可以重用，CountDownLatch不可

  ```java
  CyclicBarrier cyclicBarrier = new CyclicBarrier(int parties,Runnable barrierAction)；
  //线程内执行  先到被阻塞
  cyclicBarrier.await();
  ```

## Semaphore

* 使用场景

  * 多个共享资源的互斥使用
  * 并发线程数的控制（走一个来一个）

  ```java
  Semaphore semaphore = new Semaphore(3); //模拟3个停车位
  //线程内执行
  semaphore.acquire(); //抢到车位
  //线程内执行
  semaphore.release();
  ```

  



















