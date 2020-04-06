# [JUC中的应用场景](https://tech.meituan.com/2019/12/05/aqs-theory-and-apply.html)

| 同步工具               | 同步工具与AQS的关联                                          |
| :--------------------- | :----------------------------------------------------------- |
| ReentrantLock          | 使用AQS保存锁重复持有的次数。当一个线程获取锁时，ReentrantLock记录当前获得锁的线程标识，用于检测是否重复获取，以及错误线程试图解锁操作时异常情况的处理。 |
| Semaphore              | 使用AQS同步状态来保存信号量的当前计数。tryRelease会增加计数，acquireShared会减少计数。 |
| CountDownLatch         | 使用AQS同步状态来表示计数。计数为0时，所有的Acquire操作（CountDownLatch的await方法）才可以通过。 |
| ReentrantReadWriteLock | 使用AQS同步状态中的16位保存写锁持有的次数，剩下的16位用于保存读锁的持有次数。 |
| ThreadPoolExecutor     | Worker利用AQS同步状态实现对独占线程变量的设置（tryAcquire和tryRelease）。 |




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
  countDownLatch.await();
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























