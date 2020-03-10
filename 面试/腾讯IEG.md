# IEG

## 一面

### 线程的局部变量

* ThreadLocal与ThreadLocalMap与内存泄漏与线程池下的不安全

### 线程池参数

* corePoolSize
* maxmumPoolSize
* allowCoreThreadTimeout
* keepAliveTime
* queueCapacity
* BlockingQueue

### String、StringBuffer、StringBuilder的区别

* String不变性
  - String对象是常量，它的值不能被创建后改变，StringBuffer和StringBuilder可以可变；
  * StringBuilder非线程安全（单线程使用），String与StringBuffer线程安全（多线程使用）；
  * 如果程序不是多线程的，那么使用StringBuilder效率高于StringBuffer。

* 常量池

  常量池是一个内存空间，不同于使用new关键字创建的对象所在的堆空间。
  常量池是为了避免频繁的创建和销毁对象而影响系统性能，其实现了对象的共享。当需要一个对象时，就可以从池中取一个出来（如果池中没有则创建一个），则在需要重复创建相等变量时节省了很多时间。
  在编译期被确定，并被保存在已编译的.class文件中的一些数据，包括类、方法、接口等中的常量和字符串常量。常量池还具备动态性，运行期间可以将新的常量放入池中。java中基本类型的包装类的大部分都实现了常量池技术， 即Byte,Short,Integer,Long,Character,Boolean；

  下面代码创建了几个String对象?

```java
String s1 = new String("s1") ; 
String s2 = new String("s1") ;
```

```java
// 3个，编译期在常量池中创建1个，即“s1”常量对象；运行期堆中创建2个，即s1和s2对象。
String s1 = "s1";  
String s2 = s1;  
s2 = "s2";
```

------

### ==和equals的区别

### 重写equals()不重写hashCode()会发生什么

### volatile怎么保证可见性

### 并发编程的锁机制

* 可重入锁

* 读写锁

  读写锁将对一个资源的访问分成了2个锁，如文件，一个读锁和一个写锁。正因为有了读写锁，才使得多个线程之间的读操作不会发生冲突。`ReadWriteLock`就是读写锁，它是一个接口，ReentrantReadWriteLock实现了这个接口。可以通过readLock()获取读锁，通过writeLock()获取写锁。

* 可中断锁

  可中断锁，即可以中断的锁。在Java中，synchronized就不是可中断锁，而Lock是可中断锁。 如果某一线程A正在执行锁中的代码，另一线程B正在等待获取该锁，可能由于等待时间过长，线程B不想等待了，想先处理其他事情，我们可以让它中断自己或者在别的线程中中断它，这种就是可中断锁。

  Lock接口中的**lockInterruptibly**()方法就体现了Lock的可中断性。

* 公平锁

  `synchronized`是非公平锁，它无法保证等待的线程获取锁的顺序。对于`ReentrantLock`和`ReentrantReadWriteLock`，默认情况下是非公平锁，但是可以设置为公平锁。

### synchronized和lock的区别

- Lock是一个接口，而synchronized是Java中的关键字，synchronized是内置的语言实现；
- synchronized在发生异常时，会自动释放线程占有的锁，因此不会导致死锁现象发生；而Lock在发生异常时，如果没有主动通过unLock()去释放锁，则很可能造成死锁现象，因此使用Lock时需要在finally块中释放锁；
- Lock可以让等待锁的线程响应中断，而synchronized却不行，使用synchronized时，等待的线程会一直等待下去，不能够响应中断；
- 通过Lock可以知道有没有成功获取锁，而synchronized却无法办到。
- Lock可以提高多个线程进行读操作的效率。（可以通过readwritelock实现读写分离）
- 性能上来说，在资源竞争不激烈的情形下，Lock性能稍微比synchronized差点（编译程序通常会尽可能的进行优化synchronized）。但是当同步非常激烈的时候，synchronized的性能一下子能下降好几十倍。而ReentrantLock确还能维持常态。

 到了JDK1.6，发生了变化，对synchronize加入了很多优化措施，有自适应自旋，锁消除，锁粗化，轻量级锁，偏向锁等等。导致在JDK1.6上synchronize的性能并不比Lock差。官方也表示，他们也更支持synchronize，在未来的版本中还有优化余地，所以还是提倡在synchronized能实现需求的情况下，优先考虑使用synchronized来进行同步。

### synchronized的JVM底层实现

### sleep和wait的区别

### 锁池和等待池

### notify和notifyAll有什么区别

### 什么情况会发生死锁

### 死锁的处理方法

### Cookie和Session的区别

### 怎么防止Cookie欺骗

### 在浏览器输入域名，到浏览器显示出页面的过程

## 二面

### 谈谈对UDF的理解，

### 写UDF的目的，代码怎么写

```
5、项目里面为什么要用kafka stream做实时计算，而不是用spark或者flink，kafka sql和spark sql了解过吗
6、项目里面用到了时序数据库opentsdb，为什么要用这个，有没有跟其它的时序数据库对比过
7、平时逛不逛社区，有没有参与过开源项目
```

### 改造hive表后怎么进行数据一致性校验的，有没有自动化流程

### 讲讲kafka broker的源码里面你最熟悉的类，以及这个类的主要方法，用的什么设计模式

### 数据采集到数据可视化，如何判断数据有没有丢，若丢了如何定位到在哪一个环节

## 三面

```
1、看你写了实时计算的程序，你怎么保证计算的结果肯定是对的
2、数据接入的时候，怎么往kafka topic里面发的，用的什么方式，起了几个线程，producer是线程安全的吗
3、kafka集群有几台机器，怎么确定你们项目需要用几台机器，有评估过吗，吞吐量测过吗
4、spark streaming是怎么跟kafka交互的，具体代码怎么写的，程序执行流程是怎样的，这个过程中怎么确保数据不丢
5、kafka监控是怎么做的，kafka中能彻底删除数据吗，怎么做的
```

## 面委会（全程聊天）

- 平时是怎么学习的，爱看哪些博客，怎么看待加班，有没有成为leader的潜力

# 腾讯视频(PCG)

## 一面

### 算法

* 有序数组合并
* 前序遍历二叉树

### 智力题

* 有1000个苹果,10个篮子,问怎么把这1000个苹果放在这10个篮子里,能让任选多少个苹果都能整篮提取

### 计算机网络

* 网络编程中,客户端和端各需要调用哪些函数
* tcp协议里,怎么保证四次的最后一个包没有丢

### SQL

有三张mysql表:table_a,table_b,table_c.其中都包含字段sentence,类型为varchar,问:查询出在table_c中,但不在table_a且不在table_b的sentence

### redis如何淘汰过期的数据

