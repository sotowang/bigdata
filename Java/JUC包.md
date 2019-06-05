# JUC包分类

* 线程执行器executor
* 锁locks
* 原子变量类atomic
* 并发工具类tools
  * 闭锁 CountDownLatch
  * 栅栏 CyclicBarrier
  * 信号量 Senaphore
  * 交换器 Exchanger
* 并发集合collections
  * BlockingQueue
    * ArrayBlockingQueue
      * 一个由数组结构级顾的有界阻塞队列
    * LinkedBlockingQueue
      * 一个由链表结构组成的有界/无界阻塞队列
    * PriorityBlockingQueue
      * 一个支持优先级排序的无界阻塞队列
    * DelayQueue
      * 一个使用优先级队列米与的堍人阻塞队列
    * SynchronousQueue
      * 一个不存储元素的阻塞队列
    * LinkedTransferQueue
      * 一个由链表结构级成的无界阻塞队列
    * LinkedBlockingDeque
      * 一个由链表结构组成的双向阻塞队列