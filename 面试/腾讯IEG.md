# 一面

```
synchronized和lock的区别，synchronized的底层实现
5、sleep和wait的区别，sleep会不会释放锁，notify和notifyAll的区别
6、了不了解线程的局部变量，讲讲线程池参数
7、什么情况会发生死锁，死锁的处理方法
8、Cookie和Session的区别，怎么防止Cookie欺骗
9、从用户在浏览器输入域名，到浏览器显示出页面的过程
```

------

## String、StringBuffer、StringBuilder的区别

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

## ==和equals的区别

## 重写equals()不重写hashCode()会发生什么

## volatile怎么保证可见性

## 并发编程的锁机制

* 可重入锁

* 读写锁

  读写锁将对一个资源的访问分成了2个锁，如文件，一个读锁和一个写锁。正因为有了读写锁，才使得多个线程之间的读操作不会发生冲突。`ReadWriteLock`就是读写锁，它是一个接口，ReentrantReadWriteLock实现了这个接口。可以通过readLock()获取读锁，通过writeLock()获取写锁。

* 可中断锁

  可中断锁，即可以中断的锁。在Java中，synchronized就不是可中断锁，而Lock是可中断锁。 如果某一线程A正在执行锁中的代码，另一线程B正在等待获取该锁，可能由于等待时间过长，线程B不想等待了，想先处理其他事情，我们可以让它中断自己或者在别的线程中中断它，这种就是可中断锁。

  Lock接口中的**lockInterruptibly**()方法就体现了Lock的可中断性。

* 公平锁

  `synchronized`是非公平锁，它无法保证等待的线程获取锁的顺序。对于`ReentrantLock`和`ReentrantReadWriteLock`，默认情况下是非公平锁，但是可以设置为公平锁。

## synchronized和lock的区别

- Lock是一个接口，而synchronized是Java中的关键字，synchronized是内置的语言实现；
- synchronized在发生异常时，会自动释放线程占有的锁，因此不会导致死锁现象发生；而Lock在发生异常时，如果没有主动通过unLock()去释放锁，则很可能造成死锁现象，因此使用Lock时需要在finally块中释放锁；
- Lock可以让等待锁的线程响应中断，而synchronized却不行，使用synchronized时，等待的线程会一直等待下去，不能够响应中断；
- 通过Lock可以知道有没有成功获取锁，而synchronized却无法办到。
- Lock可以提高多个线程进行读操作的效率。（可以通过readwritelock实现读写分离）
- 性能上来说，在资源竞争不激烈的情形下，Lock性能稍微比synchronized差点（编译程序通常会尽可能的进行优化synchronized）。但是当同步非常激烈的时候，synchronized的性能一下子能下降好几十倍。而ReentrantLock确还能维持常态。

 到了JDK1.6，发生了变化，对synchronize加入了很多优化措施，有自适应自旋，锁消除，锁粗化，轻量级锁，偏向锁等等。导致在JDK1.6上synchronize的性能并不比Lock差。官方也表示，他们也更支持synchronize，在未来的版本中还有优化余地，所以还是提倡在synchronized能实现需求的情况下，优先考虑使用synchronized来进行同步。

------

## synchronized的JVM底层实现

synrhronized关键字简洁、清晰、语义明确，因此即使有了Lock接口，使用的还是非常广泛。其应用层的语义是可以把任何一个非null对象 作为"锁"，当synchronized作用在方法上时，锁住的便是对象实例（this）；当作用在静态方法时锁住的便是对象对应的Class实例，因为 Class数据存在于永久带，因此静态方法锁相当于该类的一个全局锁；当synchronized作用于某一个对象实例时，锁住的便是对应的代码块。在 HotSpot JVM实现中，锁有个专门的名字：对象监视器。 

 当多个线程同时请求某个对象监视器时，对象监视器会设置几种状态用来区分请求的线程：

```
Contention List：所有请求锁的线程将被首先放置到该竞争队列
Entry List：Contention List中那些有资格成为候选人的线程被移到Entry List
Wait Set：那些调用wait方法被阻塞的线程被放置到Wait Set
OnDeck：任何时刻最多只能有一个线程正在竞争锁，该线程称为OnDeck
Owner：获得锁的线程称为Owner
!Owner：释放锁的线程
```

下图反映了个状态转换关系：

![JVM底层又是如何实现synchronized的](http://static.open-open.com/lib/uploadImg/20121109/20121109112521_220.jpg)

新请求锁的线程将首先被加入到ConetentionList中，当某个拥有锁的线程（Owner状态）调用unlock之后，如果发现 EntryList为空则从ContentionList中移动线程到EntryList，下面说明下ContentionList和EntryList 的实现方式：

------

## sleep和wait的区别

![](https://pic2.zhimg.com/80/bb4f380c79779c9dc1aea6f0a6c10b6d_hd.jpg)

**sleep** 是让线程进入阻塞状态，一定时间之后回到非阻塞状态，从而可以重新获得 CPU。

**wait** 调用的时候需要先获得该 Object 的锁，调用 wait 后，会把当前的锁释放掉同时阻塞住；当别的线程调用该 Object 的 notify/notifyAll 之后，有可能得到 CPU，同时重新获得锁。由于有如上描述锁的设计，只要在 notify 的时候首先获得锁，就可以保证 notify 的时候或者处于 wait 线程获得锁之前，或者正在 wait，从而保证不会丢掉这次 notify 信息。

------

## Java中的notify和notifyAll有什么区别？

[notify和notifyAll的区别](https://www.zhihu.com/question/37601861)

先说两个概念：锁池和等待池

- 锁池: 假设线程A已经拥有了某个对象(注意:不是类)的锁，而其它的线程想要调用这个对象的某个synchronized方法(或者synchronized块)，由于这些线程在进入对象的synchronized方法之前必须先获得该对象的锁的拥有权，但是该对象的锁目前正被线程A拥有，所以这些线程就进入了该对象的锁池中。
- 等待池: 假设一个线程A调用了某个对象的wait()方法，线程A就会释放该对象的锁后，进入到了该对象的等待池中

> Reference：[java中的锁池和等待池 ](https://link.zhihu.com/?target=http%3A//blog.csdn.net/emailed/article/details/4689220)

然后再来说notify和notifyAll的区别

- 如果线程调用了对象的 wait()方法，那么线程便会处于该对象的**等待池**中，等待池中的线程**不会去竞争该对象的锁**。
- 当有线程调用了对象的 **notifyAll**()方法（唤醒所有 wait 线程）或 **notify**()方法（只随机唤醒一个 wait 线程），被唤醒的的线程便会进入该对象的锁池中，锁池中的线程会去竞争该对象锁。也就是说，调用了notify后只要一个线程会由等待池进入锁池，而notifyAll会将该对象等待池内的所有线程移动到锁池中，等待锁竞争
- 优先级高的线程竞争到对象锁的概率大，假若某线程没有竞争到该对象锁，它**还会留在锁池中**，唯有线程再次调用 wait()方法，它才会重新回到等待池中。而竞争到对象锁的线程则继续往下执行，直到执行完了 synchronized 代码块，它会释放掉该对象锁，这时锁池中的线程会继续竞争该对象锁。

> Reference：[线程间协作：wait、notify、notifyAll ](https://link.zhihu.com/?target=http%3A//wiki.jikexueyuan.com/project/java-concurrency/collaboration-between-threads.html)

综上，所谓唤醒线程，另一种解释可以说是将线程由等待池移动到锁池，notifyAll调用后，会将全部线程由等待池移到锁池，然后参与锁的竞争，竞争成功则继续执行，如果不成功则留在锁池等待锁被释放后再次参与竞争。而notify只会唤醒一个线程。

有了这些理论基础，后面的notify可能会导致死锁，而notifyAll则不会的例子也就好解释了

------

## 什么情况会发生死锁，死锁的处理方法

**死锁（Deadlock）**就是指两个或两个以上的进程在执行的过程中，由于竞争资源或者由于彼此通信而造成的一种阻塞的现象，若无外力作用，它们都将无法推进下去，此时称系统处于死锁状态或系统产生了死锁，这些永远在互相等待的进程称为死锁进程。

死锁发生的条件
若可能发生死锁，则必定要出现三个条件：

1. 互斥条件： 指进程对所分配的资源进行排它性使用，即在一段时间内某资源只由一个进程占用。如果此时还有其它进程请求资源，则请求者只能等待，直到占有资源的进程用完后释放资源。
2. 占有且等待条件： 指进程已经保持至少一个资源，但又提出了新的资源请求，而该资源已被其他进程占有，此时请求进程阻塞，但它不会释放自己已经占有的资源。
3. 非抢占条件： 指进程已经获得的资源，在未使用完之前，不能被剥夺，只能在使用完时由自己释放。

满足以上三个条件可能会产生死锁，但如果只有这三个条件，则不一定产生死锁。对死锁的产生，还需要第四个条件。

1. 循环等待条件 存在一个封闭的进程链，使得每个资源至少占有此链中下一个进程所需要的一个资源。

前三个条件是死锁存在的必要条件，但不是充分条件。第四个条件实际上是前三个条件的潜在结果，即假设前三个条件存在，可能发生的一系列事情会导致不可解的循环等待。这四个条件连在一起构成了死锁的充分必要条件。

## 死锁处理

有三种方法可以处理死锁。

- **第一种方法**：采用某种策略来消除条件1-4中的一个条件的出现来防止死锁**【死锁预防】**。
- **第二种方法**：基于资源分配的当前状态做动态选择来避免死锁**【死锁避免】**。
- **第三种方法**：试图去检测死锁的存在并试图从死锁中恢复出来**【死锁检测】**

------

## Cookie和Session的区别

- cookie 和session 的区别：

1、cookie数据存放在客户的浏览器上，session数据放在[服务器](https://www.baidu.com/s?wd=%E6%9C%8D%E5%8A%A1%E5%99%A8&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)上。

2、cookie不是很安全，别人可以分析存放在本地的COOKIE并进行COOKIE欺骗
   考虑到安全应当使用session。

3、session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能
   考虑到减轻服务器性能方面，应当使用COOKIE。

4、单个cookie保存的数据不能超过4K，很多浏览器都限制一个站点最多保存20个cookie。

5、所以个人建议：
   将登陆信息等重要信息存放为SESSION
   其他信息如果需要保留，可以放在COOKIE中

- Cookie

Cookie是客户端技术，程序把每个用户的数据以cookie的形式写给用户各自的浏览器。当用户使用浏览器再去访问服务器中的web资源时，就会带着各自的数据去。这样，web资源处理的就是用户各自的数据了。 

- Session

Session是服务器端技术，利用这个技术，服务器在运行时可以为每一个用户的浏览器创建一个其独享的session对象，由于session为用户浏览器独享，所以用户在访问服务器的web资源时，可以把各自的数据放在各自的session中，当用户再去访问服务器中的其它web资源时，其它web资源再从用户各自的session中取出数据为用户服务。

## 怎么防止Cookie欺骗

[防止cookies欺骗－－相关解决方案](https://blog.csdn.net/sollion/article/details/6769798)

一、网络上提供的解决方案
1、
最简单的是给Cookies加个加密算法。
保险点的是给Cookies加个时间戳和IP戳，实际就是让Cookies在同个IP下多少时间内失效。

2、
实际上是这样的，不管cookies里保存了多少个字段，最后，还要增加一个验证字段，或者称为MAC码。这个码是使用上面所有字段的内容合算出来的摘要再用一种加密算法，如3DES等使用服务器的主密钥进行加密。　这样，在从cookies得到数据后，再判断一下这个MAC码就可以知道整个cookies字段是否被篡改过。

3、
我的个人意见:(想过,正准备用上)
response.COOKIES("LOGIN")("MD5COOKIES")=MD5(服务器IP&客户IP&客户ID) ///等等，看着办吧。
然后 if MD5(服务器IP&客户IP&客户ID)<>request.COOKIES("LOGIN")("MD5COOKIES") then 立即清除全部COOKIES end if
补充：上面我写的那个，客户IP不能带上。我想可以改用网卡的MAC或硬盘的SN码。

4、
做项目的时候研究了一下，理论上应该是杜绝了cookies欺骗,我的做法如下： 
自己做session，不用服务器的session功能，大网站都是这样做的，还可以做到session 
跨站。 
用户第一次访问站点的时候，程序产生一个随机sessionID,然后以cookies的方式发给客户端， 
然后将该md5(sessionID+加上该客户断信息(如IP，端口等))和状态信息一起存入数据库， 
这样就算cookies被盗，也有99%的把握不被欺骗。

5、
要防止这个，在写网页的时候就不要相信cookies，例如用户登陆时，记录用户的账号和密码到cookies里，其他叶面就不能直接使用这个cookies，特别是数据库操作，每个叶面都要把cookies与数据库中的用户名和账号对照，如果不吻合，就清空cookies，按非会员操作，即使吻合，进行数据库操作时也要检查字段，阻断注入。

6、   

参考动网防Cookies欺骗，利用动态密码与Session+Cookies双重验证

------

## [从用户在浏览器输入域名，到浏览器显示出页面的过程](https://blog.csdn.net/qq_24147051/article/details/81115806)



---

# 二面（全程怼项目，压力面）

## [谈谈对UDF的理解，写UDF的目的，代码怎么写的](https://blog.csdn.net/WYpersist/article/details/80314352)

[UDFgithub](https://github.com/sotowang/udf)

```
Hive中有3种UDF：
	UDF：操作单个数据行，产生单个数据行；
	UDAF：操作多个数据行，产生一个数据行。
	UDTF：操作一个数据行，产生多个数据行一个表作为输出。
    
用户构建的UDF使用过程如下：
	第一步：继承UDF或者UDAF或者UDTF，实现特定的方法。
	第二步：将写好的类打包为jar。如hivefirst.jar.
	第三步：进入到Hive外壳环境中，利用add jar /home/hadoop/hivefirst.jar.注册该jar文件
	第四步：为该类起一个别名，create temporary function mylength as 'com.whut.StringLength';这里注意UDF只是为这个Hive会话临时定义的。
	第五步：在select中使用mylength();
```

```
1、看你写过UDF，谈谈对UDF的理解，写UDF的目的，代码怎么写的
2、改造hive表后怎么进行数据一致性校验的，有没有自动化流程
3、看你读过kafka源码，讲讲kafka broker的源码里面你最熟悉的类，以及这个类的主要方法，用的什么设计模式
4、项目里面从数据采集到最终的数据可视化，每个环节都有可能丢数据，怎么判断数据有没有丢，如果丢了如何定位到在哪一个环节丢的
5、项目里面为什么要用kafka stream做实时计算，而不是用spark或者flink，kafka sql和spark sql了解过吗
6、项目里面用到了时序数据库opentsdb，为什么要用这个，有没有跟其它的时序数据库对比过
7、平时逛不逛社区，有没有参与过开源项目
8、看你春招笔试的时候***作系统得了0分是怎么做到的
```

# 三面

```
1、看你写了实时计算的程序，你怎么保证计算的结果肯定是对的
2、数据接入的时候，怎么往kafka topic里面发的，用的什么方式，起了几个线程，producer是线程安全的吗
3、kafka集群有几台机器，怎么确定你们项目需要用几台机器，有评估过吗，吞吐量测过吗
4、spark streaming是怎么跟kafka交互的，具体代码怎么写的，程序执行流程是怎样的，这个过程中怎么确保数据不丢
5、kafka监控是怎么做的，kafka中能彻底删除数据吗，怎么做的
```

# 面委会（全程聊天）

- 平时是怎么学习的，爱看哪些博客，怎么看待加班，有没有成为leader的潜力