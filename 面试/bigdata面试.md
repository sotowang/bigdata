[TOC]

# 美团新到店（offer）
* 去了北京美团公司里面试，一上午面完，第二天通知高分通过
## 一面（简单的聊了聊，10min）：

```
3、垃圾收集算法
4、数据建模，星型模型和雪花模型
5、数仓层级的划分，怎么对接到mysql拿数据
```

## 二面：

```
1、sql题：写一条sql删除订单表中重复的记录
2、sql题：一张网页浏览信息表，
	有两列，一列是网页ip，一列是浏览网页的用户（比如a或者b、c、d直到z），
    求这些网页被a和b或者a和c或者b和c两两组合访问的次数
3、hive数据倾斜产生的原因，怎么解决
4、设计学生成绩管理系统，符合第三范式要求，并绘出UML图
5、算法题：斐波那契数列
6、spark程序的运行流程
7、spark streaming从kafka中读数据的两种方式
8、讲讲数据库索引，B树和B+树
9、Elasticsearch的索引，单field索引和多field的联合索引
10、linux查看某文件的大小，vim中怎么替换内容
11、海量数据的Count问题（单机），如果把大文件hash成不同的小文件，此时小文件装不下某个key对应的数据，该怎么办
12、智力题：8升水，有一个5L的杯子和3L的杯子，怎么得到4升水
```

## 三面：

```
1、osi七层模型，三次握手和四次挥手，为什么两次握手不行
2、kafka怎么保证高吞吐量，项目中有测过吞吐量吗，相比于其它MQ，为什么会选择kafka，kafka怎么保证exactly once语义
3、了解hbase吗，hbase为什么查询速度快
4、hive sql怎么转换成底层的MapReduce程序，以及shuffle的过程
5、算法题：被围绕的区域，leetcode第130题原题
6、智力题：一头母牛每年生一头小母牛，每头小母牛从第四年开始，每年也会生一头小母牛，写个公式求第n年会有多少头牛
```



# 贝壳（offer）
## 一面：

```
1、synchronized的底层实现
2、线程等待时位于哪个区域，具体讲一下
3、谈谈对kafka的理解，能讲多少讲多少
4、算法题：二分查找
5、快排的时间复杂度和空间复杂度，最优情况和最差情况分别是多少，是稳定排序吗，快排为什么快
```

## 二面：

```
1、介绍项目，项目中涉及到了一些算法，介绍一下
2、两道算法题：路径问题，leetcode上63题和64题原题
3、写正则表达式匹配电话号码
4、智力题：一张圆桌子，我和面试官轮流往桌子上放硬币（随便放），直到桌子放不下为止，最后一个放硬币的人赢，如果我先放，怎么保证我肯定赢
```

# 华为（录用排序）
## 一轮玄学面：
* 面试官是做安卓的，瞧不起大数据，觉得大数据很虚，我跟他bb了一堆。然后问我有没有女朋友，我说以前有，现在分了；问我什么时候谈的，什么时候分的，我说本科谈的，毕业分了；问我为什么要分，此处省略一万字......问我现在想没想过再谈，我说毕竟转专业过来的，想趁在校期间利用好短暂的时光提升自己的技术水平（其实因为找不到）；然后面试官说以后工作了就不好找咯，我说您说的有道理............

# 快手（待hr面）
## 一面：

```
1、jvm类加载机制，类加载器，双亲委派模型
2、java实现多线程的方式
3、spark怎么划分stage，宽窄依赖，各包括哪些***作
4、zookeeper怎么保证原子性，怎么实现分布式锁
5、写个快排，为什么要用三数取中法，好处是什么
```

---

### java实现多线程的方式

[java实现多线程的方式](https://blog.csdn.net/aboy123/article/details/38307539)

JAVA多线程实现方式主要有三种：

```
继承Thread类、实现Runnable接口、使用ExecutorService、Callable、Future实现有返回结果的多线程。
其中前两种方式线程执行完后都没有返回值，只有最后一种是带返回值的。
```

* 1、继承Thread类实现多线程


> 继承Thread类的方法尽管被我列为一种多线程实现方式，但Thread本质上也是实现了Runnable接口的一个实例，
> 它代表一个线程的实例，并且，启动线程的唯一方法就是通过Thread类的start()实例方法。start()方法是一个native方法，它将启动一个新线程，并执行run()方法。这种方式实现多线程很简单，通过自己的类直接extend Thread，并复写run()方法，就可以启动新线程并执行自己定义的run()方法。例如：

```java
public class MyThread extends Thread {
　　public void run() {
　　 System.out.println("MyThread.run()");
　　}
}

```

在合适的地方启动线程如下：

```java
MyThread myThread1 = new MyThread();
MyThread myThread2 = new MyThread();
myThread1.start();
myThread2.start();
```

* 2、实现Runnable接口方式实现多线程	


如果自己的类已经extends另一个类，就无法直接extends Thread，此时，必须实现一个Runnable接口，如下：

```java
public class MyThread extends OtherClass implements Runnable {
　　public void run() {
　　 System.out.println("MyThread.run()");
　　}
}
```

为了启动MyThread，需要首先实例化一个Thread，并传入自己的MyThread实例：

```java
MyThread myThread = new MyThread();
Thread thread = new Thread(myThread);
thread.start();
```

事实上，当传入一个Runnable target参数给Thread后，Thread的run()方法就会调用target.run()，参考JDK源代码：

```java
public void run() {
　　if (target != null) {
　　 target.run();
　　}
}
```

* 3、使用ExecutorService、Callable、Future实现有返回结果的多线程	
ExecutorService、Callable、Future这个对象实际上都是属于Executor框架中的功能类。想要详细了解Executor框架的可以访问http://www.javaeye.com/topic/366591 ，这里面对该框架做了很详细的解释。返回结果的线程是在JDK1.5中引入的新特征，确实很实用，有了这种特征我就不需要再为了得到返回值而大费周折了，而且即便实现了也可能漏洞百出。
可返回值的任务必须实现Callable接口，类似的，无返回值的任务必须Runnable接口。执行Callable任务后，可以获取一个Future的对象，在该对象上调用get就可以获取到Callable任务返回的Object了，再结合线程池接口ExecutorService就可以实现传说中有返回结果的多线程了。下面提供了一个完整的有返回结果的多线程测试例子，在JDK1.5下验证过没问题可以直接使用。代码如下：

```java
import java.util.concurrent.*;
import java.util.Date;
import java.util.List;
import java.util.ArrayList;
 
/**
* 有返回值的线程
*/
@SuppressWarnings("unchecked")
public class Test {
public static void main(String[] args) throws ExecutionException,
    InterruptedException {
   System.out.println("----程序开始运行----");
   Date date1 = new Date();
 
   int taskSize = 5;
   // 创建一个线程池
   ExecutorService pool = Executors.newFixedThreadPool(taskSize);
   // 创建多个有返回值的任务
   List<Future> list = new ArrayList<Future>();
   for (int i = 0; i < taskSize; i++) {
    Callable c = new MyCallable(i + " ");
    // 执行任务并获取Future对象
    Future f = pool.submit(c);
    // System.out.println(">>>" + f.get().toString());
    list.add(f);
   }
   // 关闭线程池
   pool.shutdown();
 
   // 获取所有并发任务的运行结果
   for (Future f : list) {
    // 从Future对象上获取任务的返回值，并输出到控制台
    System.out.println(">>>" + f.get().toString());
   }
 
   Date date2 = new Date();
   System.out.println("----程序结束运行----，程序运行时间【"
     + (date2.getTime() - date1.getTime()) + "毫秒】");
}
}
 
class MyCallable implements Callable<Object> {
private String taskNum;
 
MyCallable(String taskNum) {
   this.taskNum = taskNum;
}
 
public Object call() throws Exception {
   System.out.println(">>>" + taskNum + "任务启动");
   Date dateTmp1 = new Date();
   Thread.sleep(1000);
   Date dateTmp2 = new Date();
   long time = dateTmp2.getTime() - dateTmp1.getTime();
   System.out.println(">>>" + taskNum + "任务终止");
   return taskNum + "任务返回运行结果,当前任务时间【" + time + "毫秒】";
}
}

```

代码说明：
>上述代码中Executors类，提供了一系列工厂方法用于创先线程池，返回的线程池都实现了ExecutorService接口。	
public static ExecutorService newFixedThreadPool(int nThreads) 	
创建固定数目线程的线程池。	
public static ExecutorService newCachedThreadPool() 	
创建一个可缓存的线程池，调用execute 将重用以前构造的线程（如果线程可用）。如果现有线程没有可用的，则创建一个新线程并添加到池中。终止并从缓存中移除那些已有 60 秒钟未被使用的线程。	
public static ExecutorService newSingleThreadExecutor() 	
创建一个单线程化的Executor。	
public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize) 	
创建一个支持定时及周期性的任务执行的线程池，多数情况下可用来替代Timer类。

ExecutoreService提供了submit()方法，传递一个Callable，或Runnable，返回Future。如果Executor后台线程池还没有完成Callable的计算，这调用返回Future对象的get()方法，会阻塞直到计算完成。

---
### Spark中的宽窄依赖和Stage的划分

[Spark中的宽窄依赖和Stage的划分](https://blog.csdn.net/LHWorldBlog/article/details/79300039)

#### 前述

RDD之间有一系列的依赖关系，依赖关系又分为窄依赖和宽依赖。

Spark中的Stage其实就是一组并行的任务，任务是一个个的task 。

#### 具体细节
* 窄依赖	
父RDD和子RDD partition之间的关系是一对一的。或者父RDD一个partition只对应一个子RDD的partition情况下的父RDD和子RDD partition关系是多对一的。不会有shuffle的产生。父RDD的一个分区去到子RDD的一个分区。

---
## 二面：

```
1、sql题：找出单科成绩高于该科平均成绩的同学名单（无论该学生有多少科，只要有一科满足即可）
2、sql题：找出单科成绩高于该科平均成绩的同学名单（该学生所有科都必须满足）
3、算法题：求数组中连续子数组的最大和
4、算法题：使用最小花费爬楼梯，leetcode746题原题
```

## 三面：

```
1、讲一下java IO
2、算法题：输入n个整数，找出其中最大的k个数
3、算法题：给一个整数数组和一个目标值，找出数组中和为目标值的两个数
```

---

# 北京 快手 

## 大数据组件

```
1. 是否自己搭建的集群, 集群节点数及配置
2. Hadoop的XML配置文件有哪些, 改过哪些参数, 分别代表什么含义
3. HDFS NameNode高可用如何实现, 需要哪些角色. YARN有哪些组件, 如何分配资源
4. Spark RDD有哪些特点, 宽依赖和窄依赖. RDD的缓存级别
5. DAGScheduler及stage如何划分. 给一个比较复杂的RDD lineage, 手动划分stage和task
6. Spark Streaming以一定的时间窗口统计PV/UV, 如果窗口内数据量暴涨, 如何保证稳定性. 如果会延迟上报, 如何保证实时性
7. Kafka与Spark Streaming集成, 如何保证exactly once语义
8. Spark/Hive中大表join小表的优化方法. 数据倾斜和shuffle调优方法
9. 调整过Hive的哪些参数, 用什么执行引擎. Hive UDF怎么写, 写过哪些. HiveQL是怎样解析成MR/Spark job的
10. HBase的数据在HDFS上是怎样存储的, 写入数据的流程是怎样的. 为什么HBase适合写多读少业务
11. HBase的一个region由哪些东西组成. RegionServer宕机之后如何感知, 如何迁移数据
12. 为什么选用Kudu作为HBase和Hive的折中方案, 它有什么特点. 如果不用Kudu, HBase的二级索引能解决问题吗
13. Impala的查询及执行与Hive有什么不同 [PS. 我之前的项目里用了Kudu+Impala]
```

### Zookeeper集群节点数量为什么要是奇数个？

[Zookeeper集群节点数量为什么要是奇数个？](https://blog.csdn.net/u010476994/article/details/79806041)

首先需要明确zookeeper选举的规则：leader选举，要求 可用节点数量 > 总节点数量/2  。注意 是 > , 不是 ≥。

> 注：为什么规则要求 可用节点数量 > 集群总结点数量/2 ？  如果不这样限制，在集群出现脑裂的时候，可能会出现多个子集群同时服务的情况（即子集群各组选举出自己的leader）， 这样对整个zookeeper集群来说是紊乱的。

换句话说，如果遵守上述规则进行选举，即使出现脑裂，集群最多也只能回出现一个子集群可以提供服务的情况（能满足节点数量> 总结点数量/2 的子集群最多只会有一个）。所以要限制 可用节点数量 > 集群总结点数量/2 。

采用奇数个的节点主要是出于两方面的考虑：

#### 1、防止由脑裂造成的集群不可用。

##### 首先，什么是脑裂？

集群的脑裂通常是发生在节点之间通信不可达的情况下，集群会分裂成不同的小集群，小集群各自选出自己的master节点，导致原有的集群出现多个master节点的情况，这就是脑裂。

下面举例说一下为什么采用奇数台节点，就可以防止由于脑裂造成的服务不可用：

(1) 假如zookeeper集群有 5 个节点，发生了脑裂，脑裂成了A、B两个小集群： 

     (a) A ： 1个节点 ，B ：4个节点 ， 或 A、B互换

     (b) A ： 2个节点， B ：3个节点  ， 或 A、B互换

    可以看出，上面这两种情况下，A、B中总会有一个小集群满足 可用节点数量 > 总节点数量/2 。所以zookeeper集群仍然能够选举出leader ， 仍然能对外提供服务，只不过是有一部分节点失效了而已。

(2) 假如zookeeper集群有4个节点，同样发生脑裂，脑裂成了A、B两个小集群：

    (a) A：1个节点 ，  B：3个节点，   或 A、B互换 

    (b) A：2个节点 ， B：2个节点

    可以看出，情况(a) 是满足选举条件的，与（1）中的例子相同。 但是情况(b) 就不同了，因为A和B都是2个节点，都不满足 可用节点数量 > 总节点数量/2 的选举条件， 所以此时zookeeper就彻底不能提供服务了。

综合上面两个例子可以看出： 在节点数量是奇数个的情况下， zookeeper集群总能对外提供服务（即使损失了一部分节点）；如果节点数量是偶数个，会存在zookeeper集群不能用的可能性（脑裂成两个均等的子集群的时候）。

在生产环境中，如果zookeeper集群不能提供服务，那将是致命的 ， 所以zookeeper集群的节点数一般采用奇数个。

#### 2、在容错能力相同的情况下，奇数台更节省资源。

leader选举，要求 可用节点数量 > 总节点数量/2  。注意 是 > , 不是 ≥。

举两个例子：

(1) 假如zookeeper集群1 ，有3个节点，3/2=1.5 ,  即zookeeper想要正常对外提供服务（即leader选举成功），至少需要2个节点是正常的。换句话说，3个节点的zookeeper集群，允许有一个节点宕机。

(2) 假如zookeeper集群2，有4个节点，4/2=2 , 即zookeeper想要正常对外提供服务（即leader选举成功），至少需要3个节点是正常的。换句话说，4个节点的zookeeper集群，也允许有一个节点宕机。

那么问题就来了， 集群1与集群2都有 允许1个节点宕机 的容错能力，但是集群2比集群1多了1个节点。在相同容错能力的情况下，本着节约资源的原则，zookeeper集群的节点数维持奇数个更好一些。

---

### Hadoop的XML配置文件有哪些, 改过哪些参数, 分别代表什么含义

[技术贴】Hadoop集群配置详解](https://zhuanlan.zhihu.com/p/25472769)

**一、配置文件介绍**

在hadoop集群中，需要配置的文件主要包括四个，分别是core-site.xml、hdfs-site.xml、mapred-site.xml和yarn-site.xml，这四个文件分别是对不同组件的配置参数，主要内容如下表所示：

![img](https://pic3.zhimg.com/80/v2-dff2be66297ddbf26c60cab2b3e1d3be_hd.jpg)

**三、搭建集群配置时重要参数**

本节讨论在给定的配置文件中指定重要的参数，在四个配置文件中，最重要的参数及其解释如下：

1. core-site.xml

![img](https://pic1.zhimg.com/80/v2-05696246e7edb924addab785311809ec_hd.jpg)

2. hdfs-site.xml

![preview](https://pic2.zhimg.com/v2-a8eeb87a945f90c128824f776e4b8845_r.jpg)



3. mapred-site.xml

![img](https://pic4.zhimg.com/80/v2-5410e59ef4ae921c5ed56cc3be3e5ecf_hd.jpg)

4. yarn-site.xml

![img](https://pic1.zhimg.com/80/v2-789f67bb082eea51849ed8a339eeb8e0_hd.jpg)

---

### HDFS NameNode高可用如何实现, 需要哪些角色. 

[NameNode 高可用整体架构概述](https://www.ibm.com/developerworks/cn/opensource/os-cn-hadoop-name-node/index.html)

在 Hadoop 1.0 时代，Hadoop 的两大核心组件 HDFS NameNode 和 JobTracker 都存在着单点问题，这其中以 NameNode 的单点问题尤为严重。因为 NameNode 保存了整个 HDFS 的元数据信息，一旦 NameNode 挂掉，整个 HDFS 就无法访问，同时 Hadoop 生态系统中依赖于 HDFS 的各个组件，包括 MapReduce、Hive、Pig 以及 HBase 等也都无法正常工作，并且重新启动 NameNode 和进行数据恢复的过程也会比较耗时。这些问题在给 Hadoop 的使用者带来困扰的同时，也极大地限制了 Hadoop 的使用场景，使得 Hadoop 在很长的时间内仅能用作离线存储和离线计算，无法应用到对可用性和数据一致性要求很高的在线应用场景中。

所幸的是，在 Hadoop2.0 中，HDFS NameNode 和 YARN ResourceManger(JobTracker 在 2.0 中已经被整合到 YARN ResourceManger 之中) 的单点问题都得到了解决，经过多个版本的迭代和发展，目前已经能用于生产环境。HDFS NameNode 和 YARN ResourceManger 的高可用 (High Availability，HA) 方案基本类似，两者也复用了部分代码，但是由于 HDFS NameNode 对于数据存储和数据一致性的要求比 YARN ResourceManger 高得多，所以 HDFS NameNode 的高可用实现更为复杂一些，本文从内部实现的角度对 HDFS NameNode 的高可用机制进行详细的分析。

HDFS NameNode 的高可用整体架构如图 1 所示 (图片来源于参考文献 [1])：

*　图 1.HDFS NameNode 高可用整体架构

![img](https://www.ibm.com/developerworks/cn/opensource/os-cn-hadoop-name-node/img001.png)

从上图中，我们可以看出 NameNode 的高可用架构主要分为下面几个部分：

Active NameNode 和 Standby NameNode：两台 NameNode 形成互备，一台处于 Active 状态，为主 NameNode，另外一台处于 Standby 状态，为备 NameNode，只有主 NameNode 才能对外提供读写服务。

主备切换控制器 ZKFailoverController：ZKFailoverController 作为独立的进程运行，对 NameNode 的主备切换进行总体控制。ZKFailoverController 能及时检测到 NameNode 的健康状况，在主 NameNode 故障时借助 Zookeeper 实现自动的主备选举和切换，当然 NameNode 目前也支持不依赖于 Zookeeper 的手动主备切换。

* Zookeeper 集群：为主备切换控制器提供主备选举支持。

共享存储系统：共享存储系统是实现 NameNode 的高可用最为关键的部分，共享存储系统保存了 NameNode 在运行过程中所产生的 HDFS 的元数据。主 NameNode 和

NameNode 通过共享存储系统实现元数据同步。在进行主备切换的时候，新的主 NameNode 在确认元数据完全同步之后才能继续对外提供服务。

DataNode 节点：除了通过共享存储系统共享 HDFS 的元数据信息之外，主 NameNode 和备 NameNode 还需要共享 HDFS 的数据块和 DataNode 之间的映射关系。DataNode 会同时向主 NameNode 和备 NameNode 上报数据块的位置信息。

下面开始分别介绍 NameNode 的主备切换实现和共享存储系统的实现，在文章的最后会结合笔者的实践介绍一下在 NameNode 的高可用运维中的一些注意事项。

#### NameNode 的主备切换实现

NameNode 主备切换主要由 ZKFailoverController、HealthMonitor 和 ActiveStandbyElector 这 3 个组件来协同实现：

ZKFailoverController 作为 NameNode 机器上一个独立的进程启动 (在 hdfs 启动脚本之中的进程名为 zkfc)，启动的时候会创建 HealthMonitor 和 ActiveStandbyElector 这两个主要的内部组件，ZKFailoverController 在创建 HealthMonitor 和 ActiveStandbyElector 的同时，也会向 HealthMonitor 和 ActiveStandbyElector 注册相应的回调方法。

HealthMonitor 主要负责检测 NameNode 的健康状态，如果检测到 NameNode 的状态发生变化，会回调 ZKFailoverController 的相应方法进行自动的主备选举。

ActiveStandbyElector 主要负责完成自动的主备选举，内部封装了 Zookeeper 的处理逻辑，一旦 Zookeeper 主备选举完成，会回调 ZKFailoverController 的相应方法来进行 NameNode 的主备状态切换。

NameNode 实现主备切换的流程如图 2 所示，有以下几步：

```
1. HealthMonitor 初始化完成之后会启动内部的线程来定时调用对应 NameNode 的 HAServiceProtocol RPC 接口的方法，对 NameNode 的健康状态进行检测。
2. HealthMonitor 如果检测到 NameNode 的健康状态发生变化，会回调 ZKFailoverController 注册的相应方法进行处理。
3. 如果 ZKFailoverController 判断需要进行主备切换，会首先使用 ActiveStandbyElector 来进行自动的主备选举。
4. ActiveStandbyElector 与 Zookeeper 进行交互完成自动的主备选举。
5. ActiveStandbyElector 在主备选举完成后，会回调 ZKFailoverController 的相应方法来通知当前的 NameNode 成为主 NameNode 或备 NameNode。
6. ZKFailoverController 调用对应 NameNode 的 HAServiceProtocol RPC 接口的方法将 NameNode 转换为 Active 状态或 Standby 状态。
```

图 2.NameNode 的主备切换流程

![img](https://www.ibm.com/developerworks/cn/opensource/os-cn-hadoop-name-node/img002.png)

#### HealthMonitor 实现分析

ZKFailoverController 在初始化的时候会创建 HealthMonitor，HealthMonitor 在内部会启动一个线程来循环调用 NameNode 的 HAServiceProtocol RPC 接口的方法来检测 NameNode 的状态，并将状态的变化通过回调的方式来通知 ZKFailoverController。

HealthMonitor 主要检测 NameNode 的两类状态，分别是 HealthMonitor.State 和 HAServiceStatus。

HealthMonitor.State 是通过 HAServiceProtocol RPC 接口的 monitorHealth 方法来获取的，反映了 NameNode 节点的健康状况，主要是磁盘存储资源是否充足。HealthMonitor.State 包括下面几种状态：

- INITIALIZING：HealthMonitor 在初始化过程中，还没有开始进行健康状况检测；
- SERVICE_HEALTHY：NameNode 状态正常；
- SERVICE_NOT_RESPONDING：调用 NameNode 的 monitorHealth 方法调用无响应或响应超时；
- SERVICE_UNHEALTHY：NameNode 还在运行，但是 monitorHealth 方法返回状态不正常，磁盘存储资源不足；
- HEALTH_MONITOR_FAILED：HealthMonitor 自己在运行过程中发生了异常，不能继续检测 NameNode 的健康状况，会导致 ZKFailoverController 进程退出；

HealthMonitor.State 在状态检测之中起主要的作用，在 HealthMonitor.State 发生变化的时候，HealthMonitor 会回调 ZKFailoverController 的相应方法来进行处理，具体处理见后文 ZKFailoverController 部分所述。

而 HAServiceStatus 则是通过 HAServiceProtocol RPC 接口的 getServiceStatus 方法来获取的，主要反映的是 NameNode 的 HA 状态，包括：

- INITIALIZING：NameNode 在初始化过程中；
- ACTIVE：当前 NameNode 为主 NameNode；
- STANDBY：当前 NameNode 为备 NameNode；
- STOPPING：当前 NameNode 已停止；

HAServiceStatus 在状态检测之中只是起辅助的作用，在 HAServiceStatus 发生变化时，HealthMonitor 也会回调 ZKFailoverController 的相应方法来进行处理，具体处理见后文 ZKFailoverController 部分所述。

#### ActiveStandbyElector 实现分析

Namenode(包括 YARN ResourceManager) 的主备选举是通过 ActiveStandbyElector 来完成的，ActiveStandbyElector 主要是利用了 Zookeeper 的写一致性和临时节点机制，具体的主备选举实现如下：

**创建锁节点**

如果 HealthMonitor 检测到对应的 NameNode 的状态正常，那么表示这个 NameNode 有资格参加 Zookeeper 的主备选举。如果目前还没有进行过主备选举的话，那么相应的 ActiveStandbyElector 就会发起一次主备选举，尝试在 Zookeeper 上创建一个路径为/hadoop-ha/${dfs.nameservices}/ActiveStandbyElectorLock 的临时节点 (${dfs.nameservices} 为 Hadoop 的配置参数 dfs.nameservices 的值，下同)，Zookeeper 的写一致性会保证最终只会有一个 ActiveStandbyElector 创建成功，那么创建成功的 ActiveStandbyElector 对应的 NameNode 就会成为主 NameNode，ActiveStandbyElector 会回调 ZKFailoverController 的方法进一步将对应的 NameNode 切换为 Active 状态。而创建失败的 ActiveStandbyElector 对应的 NameNode 成为备 NameNode，ActiveStandbyElector 会回调 ZKFailoverController 的方法进一步将对应的 NameNode 切换为 Standby 状态。

**注册 Watcher 监听**

不管创建/hadoop-ha/${dfs.nameservices}/ActiveStandbyElectorLock 节点是否成功，ActiveStandbyElector 随后都会向 Zookeeper 注册一个 Watcher 来监听这个节点的状态变化事件，ActiveStandbyElector 主要关注这个节点的 NodeDeleted 事件。

**自动触发主备选举**

如果 Active NameNode 对应的 HealthMonitor 检测到 NameNode 的状态异常时， ZKFailoverController 会主动删除当前在 Zookeeper 上建立的临时节点/hadoop-ha/${dfs.nameservices}/ActiveStandbyElectorLock，这样处于 Standby 状态的 NameNode 的 ActiveStandbyElector 注册的监听器就会收到这个节点的 NodeDeleted 事件。收到这个事件之后，会马上再次进入到创建/hadoop-ha/${dfs.nameservices}/ActiveStandbyElectorLock 节点的流程，如果创建成功，这个本来处于 Standby 状态的 NameNode 就选举为主 NameNode 并随后开始切换为 Active 状态。

当然，如果是 Active 状态的 NameNode 所在的机器整个宕掉的话，那么根据 Zookeeper 的临时节点特性，/hadoop-ha/${dfs.nameservices}/ActiveStandbyElectorLock 节点会自动被删除，从而也会自动进行一次主备切换。

**防止脑裂**

Zookeeper 在工程实践的过程中经常会发生的一个现象就是 Zookeeper 客户端“假死”，所谓的“假死”是指如果 Zookeeper 客户端机器负载过高或者正在进行 JVM Full GC，那么可能会导致 Zookeeper 客户端到 Zookeeper 服务端的心跳不能正常发出，一旦这个时间持续较长，超过了配置的 Zookeeper Session Timeout 参数的话，Zookeeper 服务端就会认为客户端的 session 已经过期从而将客户端的 Session 关闭。“假死”有可能引起分布式系统常说的双主或脑裂 (brain-split) 现象。具体到本文所述的 NameNode，假设 NameNode1 当前为 Active 状态，NameNode2 当前为 Standby 状态。如果某一时刻 NameNode1 对应的 ZKFailoverController 进程发生了“假死”现象，那么 Zookeeper 服务端会认为 NameNode1 挂掉了，根据前面的主备切换逻辑，NameNode2 会替代 NameNode1 进入 Active 状态。但是此时 NameNode1 可能仍然处于 Active 状态正常运行，即使随后 NameNode1 对应的 ZKFailoverController 因为负载下降或者 Full GC 结束而恢复了正常，感知到自己和 Zookeeper 的 Session 已经关闭，但是由于网络的延迟以及 CPU 线程调度的不确定性，仍然有可能会在接下来的一段时间窗口内 NameNode1 认为自己还是处于 Active 状态。这样 NameNode1 和 NameNode2 都处于 Active 状态，都可以对外提供服务。这种情况对于 NameNode 这类对数据一致性要求非常高的系统来说是灾难性的，数据会发生错乱且无法恢复。Zookeeper 社区对这种问题的解决方法叫做 fencing，中文翻译为隔离，也就是想办法把旧的 Active NameNode 隔离起来，使它不能正常对外提供服务。

ActiveStandbyElector 为了实现 fencing，会在成功创建 Zookeeper 节点 hadoop-ha/${dfs.nameservices}/ActiveStandbyElectorLock 从而成为 Active NameNode 之后，创建另外一个路径为/hadoop-ha/${dfs.nameservices}/ActiveBreadCrumb 的持久节点，这个节点里面保存了这个 Active NameNode 的地址信息。Active NameNode 的 ActiveStandbyElector 在正常的状态下关闭 Zookeeper Session 的时候 (注意由于/hadoop-ha/${dfs.nameservices}/ActiveStandbyElectorLock 是临时节点，也会随之删除)，会一起删除节点/hadoop-ha/${dfs.nameservices}/ActiveBreadCrumb。但是如果 ActiveStandbyElector 在异常的状态下 Zookeeper Session 关闭 (比如前述的 Zookeeper 假死)，那么由于/hadoop-ha/${dfs.nameservices}/ActiveBreadCrumb 是持久节点，会一直保留下来。后面当另一个 NameNode 选主成功之后，会注意到上一个 Active NameNode 遗留下来的这个节点，从而会回调 ZKFailoverController 的方法对旧的 Active NameNode 进行 fencing，具体处理见后文 ZKFailoverController 部分所述。

#### ZKFailoverController 实现分析

ZKFailoverController 在创建 HealthMonitor 和 ActiveStandbyElector 的同时，会向 HealthMonitor 和 ActiveStandbyElector 注册相应的回调函数，ZKFailoverController 的处理逻辑主要靠 HealthMonitor 和 ActiveStandbyElector 的回调函数来驱动。

**对 HealthMonitor 状态变化的处理**

如前所述，HealthMonitor 会检测 NameNode 的两类状态，HealthMonitor.State 在状态检测之中起主要的作用，ZKFailoverController 注册到 HealthMonitor 上的处理 HealthMonitor.State 状态变化的回调函数主要关注 SERVICE_HEALTHY、SERVICE_NOT_RESPONDING 和 SERVICE_UNHEALTHY 这 3 种状态：

- 如果检测到状态为 SERVICE_HEALTHY，表示当前的 NameNode 有资格参加 Zookeeper 的主备选举，如果目前还没有进行过主备选举的话，ZKFailoverController 会调用 ActiveStandbyElector 的 joinElection 方法发起一次主备选举。
- 如果检测到状态为 SERVICE_NOT_RESPONDING 或者是 SERVICE_UNHEALTHY，就表示当前的 NameNode 出现问题了，ZKFailoverController 会调用 ActiveStandbyElector 的 quitElection 方法删除当前已经在 Zookeeper 上建立的临时节点退出主备选举，这样其它的 NameNode 就有机会成为主 NameNode。

而 HAServiceStatus 在状态检测之中仅起辅助的作用，在 HAServiceStatus 发生变化时，ZKFailoverController 注册到 HealthMonitor 上的处理 HAServiceStatus 状态变化的回调函数会判断 NameNode 返回的 HAServiceStatus 和 ZKFailoverController 所期望的是否一致，如果不一致的话，ZKFailoverController 也会调用 ActiveStandbyElector 的 quitElection 方法删除当前已经在 Zookeeper 上建立的临时节点退出主备选举。

**对 ActiveStandbyElector 主备选举状态变化的处理**

在 ActiveStandbyElector 的主备选举状态发生变化时，会回调 ZKFailoverController 注册的回调函数来进行相应的处理：

- 如果 ActiveStandbyElector 选主成功，那么 ActiveStandbyElector 对应的 NameNode 成为主 NameNode，ActiveStandbyElector 会回调 ZKFailoverController 的 becomeActive 方法，这个方法通过调用对应的 NameNode 的 HAServiceProtocol RPC 接口的 transitionToActive 方法，将 NameNode 转换为 Active 状态。
- 如果 ActiveStandbyElector 选主失败，那么 ActiveStandbyElector 对应的 NameNode 成为备 NameNode，ActiveStandbyElector 会回调 ZKFailoverController 的 becomeStandby 方法，这个方法通过调用对应的 NameNode 的 HAServiceProtocol RPC 接口的 transitionToStandby 方法，将 NameNode 转换为 Standby 状态。
- 如果 ActiveStandbyElector 选主成功之后，发现了上一个 Active NameNode 遗留下来的/hadoop-ha/${dfs.nameservices}/ActiveBreadCrumb 节点 (见“ActiveStandbyElector 实现分析”一节“防止脑裂”部分所述)，那么 ActiveStandbyElector 会首先回调 ZKFailoverController 注册的 fenceOldActive 方法，尝试对旧的 Active NameNode 进行 fencing，在进行 fencing 的时候，会执行以下的操作：

1. 首先尝试调用这个旧 Active NameNode 的 HAServiceProtocol RPC 接口的 transitionToStandby 方法，看能不能把它转换为 Standby 状态。
2. 如果 transitionToStandby 方法调用失败，那么就执行 Hadoop 配置文件之中预定义的隔离措施，Hadoop 目前主要提供两种隔离措施，通常会选择 sshfence：

- sshfence：通过 SSH 登录到目标机器上，执行命令 fuser 将对应的进程杀死；
- shellfence：执行一个用户自定义的 shell 脚本来将对应的进程隔离；

只有在成功地执行完成 fencing 之后，选主成功的 ActiveStandbyElector 才会回调 ZKFailoverController 的 becomeActive 方法将对应的 NameNode 转换为 Active 状态，开始对外提供服务。

#### NameNode 的共享存储实现

过去几年中 Hadoop 社区涌现过很多的 NameNode 共享存储方案，比如 shared NAS+NFS、BookKeeper、BackupNode 和 QJM(Quorum Journal Manager) 等等。目前社区已经把由 Clouderea 公司实现的基于 QJM 的方案合并到 HDFS 的 trunk 之中并且作为默认的共享存储实现，本部分只针对基于 QJM 的共享存储方案的内部实现原理进行分析。为了理解 QJM 的设计和实现，首先要对 NameNode 的元数据存储结构有所了解。

##### NameNode 的元数据存储概述

一个典型的 NameNode 的元数据存储目录结构如图 3 所示 (图片来源于参考文献 [4])，这里主要关注其中的 EditLog 文件和 FSImage 文件：

图 3 .NameNode 的元数据存储目录结构

![img](https://www.ibm.com/developerworks/cn/opensource/os-cn-hadoop-name-node/img003.png)

NameNode 在执行 HDFS 客户端提交的创建文件或者移动文件这样的写操作的时候，会首先把这些操作记录在 EditLog 文件之中，然后再更新内存中的文件系统镜像。内存中的文件系统镜像用于 NameNode 向客户端提供读服务，而 EditLog 仅仅只是在数据恢复的时候起作用。记录在 EditLog 之中的每一个操作又称为一个事务，每个事务有一个整数形式的事务 id 作为编号。EditLog 会被切割为很多段，每一段称为一个 Segment。正在写入的 EditLog Segment 处于 in-progress 状态，其文件名形如 edits_inprogress_${start_txid}，其中${start_txid} 表示这个 segment 的起始事务 id，例如上图中的 edits_inprogress_0000000000000000020。而已经写入完成的 EditLog Segment 处于 finalized 状态，其文件名形如 edits_${start_txid}-${end_txid}，其中${start_txid} 表示这个 segment 的起始事务 id，${end_txid} 表示这个 segment 的结束事务 id，例如上图中的 edits_0000000000000000001-0000000000000000019。

NameNode 会定期对内存中的文件系统镜像进行 checkpoint 操作，在磁盘上生成 FSImage 文件，FSImage 文件的文件名形如 fsimage_${end_txid}，其中${end_txid} 表示这个 fsimage 文件的结束事务 id，例如上图中的 fsimage_0000000000000000020。在 NameNode 启动的时候会进行数据恢复，首先把 FSImage 文件加载到内存中形成文件系统镜像，然后再把 EditLog 之中 FsImage 的结束事务 id 之后的 EditLog 回放到这个文件系统镜像上。

#### NameNode 高可用运维中的注意事项

本节结合笔者的实践，从初始化部署和日常运维两个方面介绍一些在 NameNode 高可用运维中的注意事项。

##### 初始化部署

如果在开始部署 Hadoop 集群的时候就启用 NameNode 的高可用的话，那么相对会比较容易。但是如果在采用传统的单 NameNode 的架构运行了一段时间之后，升级为 NameNode 的高可用架构的话，就要特别注意在升级的时候需要按照以下的步骤进行操作：

```
1. 对 Zookeeper 进行初始化，创建 Zookeeper 上的/hadoop-ha/${dfs.nameservices} 节点。创建节点是为随后通过 Zookeeper 进行主备选举做好准备，在进行主备选举的时候会在这个节点下面创建子节点 (具体可参照“ActiveStandbyElector 实现分析”一节的叙述)。这一步通过在原有的 NameNode 上执行命令 hdfs zkfc -formatZK 来完成。
2. 启动所有的 JournalNode，这通过脚本命令 hadoop-daemon.sh start journalnode 来完成。
3. 对 JouranlNode 集群的共享存储目录进行格式化，并且将原有的 NameNode 本地磁盘上最近一次 checkpoint 操作生成 FSImage 文件 (具体可参照“NameNode 的元数据存储概述”一节的叙述) 之后的 EditLog 拷贝到 JournalNode 集群上的共享目录之中，这通过在原有的 NameNode 上执行命令 hdfs namenode -initializeSharedEdits 来完成。
4. 启动原有的 NameNode 节点，这通过脚本命令 hadoop-daemon.sh start namenode 完成。
5. 对新增的 NameNode 节点进行初始化，将原有的 NameNode 本地磁盘上最近一次 checkpoint 操作生成 FSImage 文件拷贝到这个新增的 NameNode 的本地磁盘上，同时需要验证 JournalNode 集群的共享存储目录上已经具有了这个 FSImage 文件之后的 EditLog(已经在第 3 步完成了)。这一步通过在新增的 NameNode 上执行命令 hdfs namenode -bootstrapStandby 来完成。
6. 启动新增的 NameNode 节点，这通过脚本命令 hadoop-daemon.sh start namenode 完成。
7. 在这两个 NameNode 上启动 zkfc(ZKFailoverController) 进程，谁通过 Zookeeper 选主成功，谁就是主 NameNode，另一个为备 NameNode。这通过脚本命令 hadoop-daemon.sh start zkfc 完成。
```



##### 日常维护

笔者在日常的维护之中主要遇到过下面两种问题：

Zookeeper 过于敏感：Hadoop 的配置项中 Zookeeper 的 session timeout 的配置参数 ha.zookeeper.session-timeout.ms 的默认值为 5000，也就是 5s，这个值比较小，会导致 Zookeeper 比较敏感，可以把这个值尽量设置得大一些，避免因为网络抖动等原因引起 NameNode 进行无谓的主备切换。

单台 JouranlNode 故障时会导致主备无法切换：在理论上，如果有 3 台或者更多的 JournalNode，那么挂掉一台 JouranlNode 应该仍然可以进行正常的主备切换。但是笔者在某次 NameNode 重启的时候，正好赶上一台 JournalNode 挂掉宕机了，这个时候虽然某一台 NameNode 通过 Zookeeper 选主成功，但是这台被选为主的 NameNode 无法成功地从 Standby 状态切换为 Active 状态。事后追查原因发现，被选为主的 NameNode 卡在退出 Standby 状态的最后一步，这个时候它需要等待到 JournalNode 的请求全部完成之后才能退出。但是由于有一台 JouranlNode 宕机，到这台 JournalNode 的请求都积压在一起并且在不断地进行重试，同时在 Hadoop 的配置项中重试次数的默认值非常大，所以就会导致被选为主的 NameNode 无法及时退出 Standby 状态。这个问题主要是 Hadoop 内部的 RPC 通信框架的设计缺陷引起的，Hadoop HA 的源代码 IPCLoggerChannel 类中有关于这个问题的 TODO，但是截止到社区发布的 2.7.1 版本这个问题仍然存在。























---

### YARN有哪些组件, 如何分配资源

[Hadoop yarn资源调度——主要组件及介绍](https://blog.csdn.net/Fenggms/article/details/82902747)

#### yarn的概述

yarn是hadoop集群当中的资源管理系统模块，从hadoop2.x开始引入yarn来进行管理集群当中的资源（主要是服务器的各种硬件资源，包括CPU，内存，磁盘，网络IO等）以及运行在yarn上面的各种任务。
yarn主要就是为了调度资源，管理任务等。其调度分为两个层级来说：
一级调度管理：
计算资源管理
App生命周期管理
二级调度管理：
App内部的计算模型管理
多样化的计算模型

#### yarn的主要组件

各个组件介绍

ResourceManager：

```
yarn集群的主节点，主要用于接收客户端提交的任务，并对任务进行分配。
在每个集群中，仅有一个，负责集群资源的统一管理和调度。
```

NodeManager：

```
yarn集群的从节点，主要用于任务的计算。
每个节点都有一个，负责单节点资源管理和调度。
```

ApplicationMaster：

```
当有新的任务提交到ResourceManager的时候，ResourceManager会在某个从节点nodeManager上面启动一个ApplicationMaster进程，负责这个任务执行的资源的分配，任务的生命周期的监控等
```

Container：

```
资源的分配单位，ApplicationMaster启动之后，与ResourceManager进行通信，向ResourceManager提出资源申请的请求，然后ResourceManager将资源分配给ApplicationMaster，这些资源的表示，就是一个个的container.
```

其他服务：
JobHistoryServer：这是yarn提供的一个查看已经完成的任务的历史日志记录的服务，我们可以启动jobHistoryServer来观察已经完成的任务的所有详细日志信息

TimeLineServer：hadoop2.4.0以后出现的新特性，主要是为了监控所有运行在yarn平台上面的所有任务（例如MR，Storm，Spark，HBase等等）

#### yarn当中各个主要组件的作用

resourceManager主要作用：

```
处理客户端请求
启动/监控ApplicationMaster
监控NodeManager
集群的资源分配与调度
```

NodeManager主要作用：

```
单个节点上的资源分配与任务调度
接收并处理来自resourceManager的命令
接收并处理来自ApplicationMaster的命令
管理抽象容器container
定时向RM汇报本节点资源使用情况和各个container的运行状态
```

ApplicationMaster主要作用：

```
数据切分
为应用程序申请资源
任务监控与容错
负责协调来自ResourceManager的资源，开通NodeManager监视容的执行和资源使用（CPU,内存等的资源分配）
```

Container主要作用：

```
对任务运行环境的抽象
任务运行资源（节点，内存，cpu）
任务启动命令
任务运行环境
```

yarn的官网文档
http://hadoop.apache.org/docs/r2.7.5/hadoop-yarn/hadoop-yarn-site/YARN.html

yarn监控界面
yarn集群的监控管理界面：
http://node01:8088/cluster
jobHistoryServer查看界面：

http://node01:19888/jobhistory

---

### Spark RDD有哪些特点, 宽依赖和窄依赖. 

见下面

---

### RDD的缓存级别

[Spark RDD的缓存 rdd.cache() 和 rdd.persist()](https://blog.csdn.net/sunspeedzy/article/details/69055072)

顺便看一下RDD都有哪些缓存级别，查看 StorageLevel 类的源码：

```scala
object StorageLevel {
  val NONE = new StorageLevel(false, false, false, false)
  val DISK_ONLY = new StorageLevel(true, false, false, false)
  val DISK_ONLY_2 = new StorageLevel(true, false, false, false, 2)
  val MEMORY_ONLY = new StorageLevel(false, true, false, true)
  val MEMORY_ONLY_2 = new StorageLevel(false, true, false, true, 2)
  val MEMORY_ONLY_SER = new StorageLevel(false, true, false, false)
  val MEMORY_ONLY_SER_2 = new StorageLevel(false, true, false, false, 2)
  val MEMORY_AND_DISK = new StorageLevel(true, true, false, true)
  val MEMORY_AND_DISK_2 = new StorageLevel(true, true, false, true, 2)
  val MEMORY_AND_DISK_SER = new StorageLevel(true, true, false, false)
  val MEMORY_AND_DISK_SER_2 = new StorageLevel(true, true, false, false, 2)
  val OFF_HEAP = new StorageLevel(false, false, true, false)
  ......
}
```

查看其构造函数

```scala
class StorageLevel private(
    private var _useDisk: Boolean,
    private var _useMemory: Boolean,
    private var _useOffHeap: Boolean,
    private var _deserialized: Boolean,
    private var _replication: Int = 1)
  extends Externalizable {
  ......
  def useDisk: Boolean = _useDisk
  def useMemory: Boolean = _useMemory
  def useOffHeap: Boolean = _useOffHeap
  def deserialized: Boolean = _deserialized
  def replication: Int = _replication
  ......
}
```

可以看到StorageLevel类的主构造器包含了5个参数：

```
useDisk：使用硬盘（外存） 
useMemory：使用内存 
useOffHeap：使用堆外内存，这是Java虚拟机里面的概念，堆外内存意味着把内存对象分配在Java虚拟机的堆以外的内存，这些内存直接受操作系统管理（而不是虚拟机）。这样做的结果就是能保持一个较小的堆，以减少垃圾收集对应用的影响。 
deserialized：反序列化，其逆过程序列化（Serialization）是java提供的一种机制，将对象表示成一连串的字节；而反序列化就表示将字节恢复为对象的过程。序列化是对象永久化的一种机制，可以将对象及其属性保存起来，并能在反序列化后直接恢复这个对象。序列化方式存储对象可以节省磁盘或内存的空间，一般 序列化：反序列化=1:3 
replication：备份数（在多个节点上备份） 
```


理解了这5个参数，StorageLevel 的12种缓存级别就不难理解了。

val MEMORY_AND_DISK_SER_2 = new StorageLevel(true, true, false, false, 2) 就表示使用这种缓存级别的RDD将存储在硬盘以及内存中，使用序列化（在硬盘中），并且在多个节点上备份2份（正常的RDD只有一份）

另外还注意到有一种特殊的缓存级别

```scala
val OFF_HEAP = new StorageLevel(false, false, true, false)
```

使用了堆外内存，StorageLevel 类的源码中有一段代码可以看出这个的特殊性，它不能和其它几个参数共存。

#### Spark RDD Cache

允许将RDD缓存到内存中或磁盘上， 以便于重用 
Spark提供了多种缓存级别， 以便于用户根据实际需求进行调整 
Spark RDD cache/persist

#### RDD cache使用

```scala
val data = sc.textFile(“hdfs://nn:8020/input”)
data.cache() //实际上是data.persist(StorageLevel.MEMORY_ONLY)
//data.persist(StorageLevel.DISK_ONLY_2)123
```

#### cache和persist的区别

基于Spark 1.4.1 的源码，可以看到

```scala
/** Persist this RDD with the default storage level (`MEMORY_ONLY`). */
def cache(): this.type = persist()12
```

说明是cache()调用了persist(), 想要知道二者的不同还需要看一下persist函数：

```scala
/** Persist this RDD with the default storage level (`MEMORY_ONLY`). */
def persist(): this.type = persist(StorageLevel.MEMORY_ONLY)12
```

可以看到persist()内部调用了persist(StorageLevel.MEMORY_ONLY)，继续深入：

```scala

/**
 * Set this RDD's storage level to persist its values across operations after the first time
 * it is computed. This can only be used to assign a new storage level if the RDD does not
 * have a storage level set yet..
 */
def persist(newLevel: StorageLevel): this.type = {
  // TODO: Handle changes of StorageLevel
  if (storageLevel != StorageLevel.NONE && newLevel != storageLevel) {
    throw new UnsupportedOperationException(
      "Cannot change storage level of an RDD after it was already assigned a level")
  }
  sc.persistRDD(this)
  // Register the RDD with the ContextCleaner for automatic GC-based cleanup
  sc.cleaner.foreach(_.registerRDDForCleanup(this))
  storageLevel = newLevel
  this
}
```

可以看出来persist有一个 StorageLevel 类型的参数，这个表示的是RDD的缓存级别。

至此便可得出cache和persist的区别了：cache只有一个默认的缓存级别MEMORY_ONLY ，而persist可以根据情况设置其它的缓存级别。

---

### DAGScheduler及stage如何划分. 给一个比较复杂的RDD lineage, 手动划分stage和task

[[Spark源码剖析] DAGScheduler划分stage](https://www.jianshu.com/p/50c5c1032206)

#### 划分stage源码剖析

> 本文基于Spark 1.3.1

先上一些stage相关的知识点：

 ```
   1. DAGScheduler将Job分解成具有前后依赖关系的多个stage
   2. DAGScheduler是根据ShuffleDependency划分stage的
   3. stage分为ShuffleMapStage和ResultStage；一个Job中包含一个ResultStage及多个ShuffleMapStage
   4. 一个stage包含多个tasks，task的个数即该stage的finalRDD的partition数
   5. 一个stage中的task完全相同，ShuffleMapStage包含的都是ShuffleMapTask；ResultStage包含的都是ResultTask
 ```

下图为整个划分stage的函数调用关系图

![img](https://upload-images.jianshu.io/upload_images/204749-c24af52fc674f8d3.png?imageMogr2/auto-orient/)

DAGScheduler划分stage函数调用关系.png

在DAGScheduler内部通过post一个JobSubmitted事件来触发Job的提交

```scala
DAGScheduler.eventProcessLoop.post( JobSubmitted(...) )
DAGScheduler.handleJobSubmitted
```

既然这两个方法都是DAGScheduler内部的实现，为什么不直接调用函数而要这样“多此一举”呢？我猜想这是为了保证整个系统事件模型的完整性。

DAGScheduler.handleJobSubmitted部分源码及如下

```scala
private[scheduler] def handleJobSubmitted(jobId: Int,
      finalRDD: RDD[_],
      func: (TaskContext, Iterator[_]) => _,
      partitions: Array[Int],
      allowLocal: Boolean,
      callSite: CallSite,
      listener: JobListener,
      properties: Properties) {
    var finalStage: ResultStage = null
    try {
      //< 创建finalStage可能会抛出一个异常, 比如, jobs是基于一个HadoopRDD的但这个HadoopRDD已被删除
      finalStage = newResultStage(finalRDD, partitions.size, jobId, callSite)
    } catch {
      case e: Exception => 
        return
    }
    
    //< 此处省略n行代码
  }
```

该函数通过调用newResultStage函数来创建唯一的ResultStage，也就是finalStage。调用newResultStage时，传入了finalRDD、partitions.size等参数。

跟进到`DAGScheduler.newResultStage`

```scala
  private def newResultStage(
      rdd: RDD[_],
      numTasks: Int,
      jobId: Int,
      callSite: CallSite): ResultStage = {
    val (parentStages: List[Stage], id: Int) = getParentStagesAndId(rdd, jobId)
    val stage: ResultStage = new ResultStage(id, rdd, numTasks, parentStages, jobId, callSite)

    stageIdToStage(id) = stage
    updateJobIdStageIdMaps(jobId, stage)
    stage
  }
```

DAGScheduler.newResultStage首先调用`val (parentStages: List[Stage], id: Int) = getParentStagesAndId(rdd, jobId)`，这个调用看起来像是要先确定好该ResultStage依赖的父stages，

**问题1：那么是直接父stage呢？还是父stage及间接依赖的所有父stage呢？**记住这个问题，继续往下看。

跟进到`DAGScheduler.getParentStagesAndId`:

```scala
  private def getParentStagesAndId(rdd: RDD[_], jobId: Int): (List[Stage], Int) = {
    val parentStages = getParentStages(rdd, jobId)
    val id = nextStageId.getAndIncrement()      //< 这个调用确定了每个stage的id，划分stage时，会从右到左，因为是递归调用，其实越左的stage创建时，越早调到?try
    (parentStages, id)
  }
```

该函数调用`getParentStages`获得parentStages，之后获取一个递增的id，连同刚获得的parentStages一同返回，并在newResultStage中，将id作为ResultStage的id。那么，
 **问题2：stage id是父stage的大还是子stage的大？**。继续跟进源码，所有提问均会在后面解答。

跟到`getParentStages`里

```scala
  //< 这个函数的实现方式比较巧妙
  private def getParentStages(rdd: RDD[_], jobId: Int): List[Stage] = {
    //< 通过vist一级一级vist得到的父stage
    val parents = new HashSet[Stage]
    //< 已经visted的rdd
    val visited = new HashSet[RDD[_]]
    val waitingForVisit = new Stack[RDD[_]]
    def visit(r: RDD[_]) {
      if (!visited(r)) {
        visited += r

        for (dep <- r.dependencies) {
          dep match {
            //< 若为宽依赖，调用getShuffleMapStage
            case shufDep: ShuffleDependency[_, _, _] => 
              parents += getShuffleMapStage(shufDep, jobId)
            case _ =>
              //< 若为窄依赖，将该依赖中的rdd加入到待vist栈，以保证能一级一级往上vist，直至遍历整个DAG图
              waitingForVisit.push(dep.rdd)
          }
        }
      }
    }
    waitingForVisit.push(rdd)
    while (waitingForVisit.nonEmpty) {
      visit(waitingForVisit.pop())
    }
    parents.toList
  }
```

 函数getParentStages中，遍历整个RDD依赖图的finalRDD的List[dependency] （关于RDD及依赖，可参考[举例说明Spark RDD的分区、依赖](https://www.jianshu.com/p/6b9e4001723d)，若遇到ShuffleDependency（即宽依赖），则调用`getShuffleMapStage(shufDep, jobId)`返回一个`ShuffleMapStage`类型对象，添加到父stage列表中。若为NarrowDenpendency，则将该NarrowDenpendency包含的RDD加入到待visit队列中，之后继续遍历待visit队列中的RDD，直到遇到ShuffleDependency或无依赖的RDD。

函数`getParentStages`的职责说白了就是：以参数rdd为起点，一级一级遍历依赖，碰到窄依赖就继续往前遍历，碰到宽依赖就调用`getShuffleMapStage(shufDep, jobId)`。这里需要特别注意的是，`getParentStages`以rdd为起点遍历RDD依赖并不会遍历整个RDD依赖图，而是一级一级遍历直到所有“遍历路线”都碰到了宽依赖就停止。剩下的事，在遍历的过程中交给`getShuffleMapStage`。

那么，让我来看看函数`getShuffleMapStage`的实现：

```scala
private def getShuffleMapStage(
    shuffleDep: ShuffleDependency[_, _, _],
    jobId: Int): ShuffleMapStage = {
  shuffleToMapStage.get(shuffleDep.shuffleId) match {
    case Some(stage) => stage
    case None =>
      // We are going to register ancestor shuffle dependencies
      registerShuffleDependencies(shuffleDep, jobId)

      //< 然后创建新的ShuffleMapStage
      val stage = newOrUsedShuffleStage(shuffleDep, jobId)
      shuffleToMapStage(shuffleDep.shuffleId) = stage

      stage
  }
}
```

在划分stage的过程中，由于每次`shuffleDep.shuffleId`都不同且都是第一次出现，显然`shuffleToMapStage.get(shuffleDep.shuffleId)`会match到None，便会调用`newOrUsedShuffleStage`。来看看它的实现：

```scala
private def registerShuffleDependencies(shuffleDep: ShuffleDependency[_, _, _], jobId: Int) {
    val parentsWithNoMapStage = getAncestorShuffleDependencies(shuffleDep.rdd)
    while (parentsWithNoMapStage.nonEmpty) {
      //< 出栈的其实是shuffleDep的前一个宽依赖，且shuffleToMapStage不包含以该出栈宽依赖id为key的元素
      val currentShufDep = parentsWithNoMapStage.pop()
      //< 创建新的ShuffleMapStage
      val stage = newOrUsedShuffleStage(currentShufDep, jobId)
      //< 将新创建的ShuffleMapStage加入到shuffleId -> ShuffleMapStage映射关系中
      shuffleToMapStage(currentShufDep.shuffleId) = stage
    }
  }
```

函数`registerShuffleDependencies`首先调用`getAncestorShuffleDependencies`，这个函数遍历参数rdd的List[dependency]，若遇到ShuffleDependency，加入到`parents: Stack[ShuffleDependency[_, _, _]]`中；若遇到窄依赖，则遍历该窄依赖对应rdd的父一层依赖，知道遇到宽依赖为止。实现与`getParentStages`基本一致，不同的是这里是将宽依赖加入到parents中并返回。

registerShuffleDependencies拿到各个“依赖路线”最近的所有宽依赖后。对每个宽依赖调用`newOrUsedShuffleStage`，该函数用来创建新ShuffleMapStage或获得已经存在的ShuffleMapStage。来看它的实现： 

```scala
private def newOrUsedShuffleStage(
      shuffleDep: ShuffleDependency[_, _, _],
      jobId: Int): ShuffleMapStage = {
    val rdd = shuffleDep.rdd
    val numTasks = rdd.partitions.size
    val stage = newShuffleMapStage(rdd, numTasks, shuffleDep, jobId, rdd.creationSite)
    //< 若该shuffleDep.shulleId对应的, stage已经在MapOutputTracker中存在，那么可用的输出的数量及位置将从MapOutputTracker恢复
    if (mapOutputTracker.containsShuffle(shuffleDep.shuffleId)) {
      val serLocs = mapOutputTracker.getSerializedMapOutputStatuses(shuffleDep.shuffleId)
      val locs = MapOutputTracker.deserializeMapStatuses(serLocs)
      for (i <- 0 until locs.size) {
        stage.outputLocs(i) = Option(locs(i)).toList // locs(i) will be null if missing
      }
      stage.numAvailableOutputs = locs.count(_ != null)
    } else {
      // Kind of ugly: need to register RDDs with the cache and map output tracker here
      // since we can't do it in the RDD constructor because # of partitions is unknown
      logInfo("Registering RDD " + rdd.id + " (" + rdd.getCreationSite + ")")
      //< 否则使用shuffleDep.shuffleId, rdd.partitions.size在mapOutputTracker中注册，这会在shuffle阶段reducer从shuffleMap端fetch数据起作用
      mapOutputTracker.registerShuffle(shuffleDep.shuffleId, rdd.partitions.size)
    }
    stage
  }
```

函数`newOrUsedShuffleStage`首先调用`newShuffleMapStage`来创建新的ShuffleMapStage，来看下`newShuffleMapStage`的实现：

```scala
  private def newShuffleMapStage(
      rdd: RDD[_],
      numTasks: Int,
      shuffleDep: ShuffleDependency[_, _, _],
      jobId: Int,
      callSite: CallSite): ShuffleMapStage = {
    val (parentStages: List[Stage], id: Int) = getParentStagesAndId(rdd, jobId)
    val stage: ShuffleMapStage = new ShuffleMapStage(id, rdd, numTasks, parentStages,
      jobId, callSite, shuffleDep)

    stageIdToStage(id) = stage
    updateJobIdStageIdMaps(jobId, stage)
    stage
  }
```

结合文章开始处的函数调用关系图，可以看到`newShuffleMapStage`竟然又调用`getParentStagesAndId`来获取它的parentStages。那么，文章开头处的整个函数调用流程又会继续走一遍，不同的是起点rdd不是原来的finalRDD而是变成了这里的宽依赖的rdd。

静下心来，仔细看几遍上文提到的源码及注释，其实每一次的如上图所示的递归调用，其实就只做了两件事：

 

1. 遍历起点RDD的依赖列表，若遇到窄依赖，则继续遍历该窄依赖的父List[RDD]的依赖，直到碰到宽依赖；若碰到宽依赖（不管是起点RDD的宽依赖还是遍历多级依赖碰到的宽依赖），则以宽依赖RDD为起点再次重复上述过程。直到到达RDD依赖图的最左端，也就是遍历到了没有依赖的RDD，则进入2
2. 达到RDD依赖图的最左端，即递归调用也到了最深得层数，`getParentStagesAndId中`，`getParentStages`第一次返回（第一次返回为空，因为最初的stage没有父stage），`val id = nextStageId.getAndIncrement()`也是第一次被调用，获得第一个stage的id，为0（注意，这个时候还没有创建第一个stage）。这之后，便调用

```scala
val stage: ShuffleMapStage = new ShuffleMapStage(id, rdd, numTasks, parentStages, jobId, callSite, shuffleDep)
```

 创建第一个ShuffleMapStage。至此，这一层递归调用结束，返回到上一层递归中，这一层创建的所有的ShuffleMapStage会作为下一层stage的父List[stage]用来构造上一层的stages。上一层递归调用返回后，上一层创建的stage又将作为上上层的parent List[stage]来构造上上层的stages。依次类推，直到最后的ResultStage也被创建出来为止。整个stage的划分完成。

有一个需要注意的点是，无论对于ShuffleMapStage还是ResultStage来说，task的个数即该stage的finalRDD的partition的个数，仔细查看下上文中的`newResultStage`和`newShuffleMapStage`函数可以搞明白这点，不再赘述。

最后，解答下上文中的两个问题：
 **问题1：**每个stage都有一个val parents: List[Stage]成员，保存的是其直接依赖的父stages；其直接父stages又有自身依赖的父stages，以此类推，构成了整个DAG图

**问题2：**父stage的id比子stage的id小，DAG图中，越左边的stage，id越小。

---

### Spark Streaming以一定的时间窗口统计PV/UV, 如果窗口内数据量暴涨, 如何保证稳定性. 如果会延迟上报, 如何保证实时性

---

### Kafka与Spark Streaming集成, 如何保证exactly once语义

[Spark Streaming消费kafka使用及原理](https://zhuanlan.zhihu.com/p/30721699)

本文主要介绍Spark Streaming(以下简称SS，版本1.6.3)的一些基本概念，以及SS消费kafka(版本0.8.2.1)数据的两种方式的使用及其原理。我会对这两种方案做详细的解析，同时对比两种方案优劣，以及针对Direct Approach (No Receivers)模式介绍其如何实现Exactly Once Semantics，也就是保证接收到的数据只被处理一次，不丢，不重。

SS是Spark上的一个流式处理框架，可以面向海量数据实现高吞吐量、高容错的实时计算。SS支持多种类型数据源，包括Kafka、Flume、twitter、zeroMQ、Kinesis以及TCP sockets等。SS实时接收数据流，并按照一定的时间间隔（下文称为“批处理时间间隔”）将连续的数据流拆分成一批批离散的数据集；然后应用诸如map、reduce、join和window等丰富的API进行复杂的数据处理；最后提交给Spark引擎进行运算，得到批量结果数据，因此其也被称为准实时处理系统。而结果也能保存在很多地方，如HDFS，数据库等。另外SS也能和MLlib（机器学习）以及GraphX（图计算）完美融合。

![img](https://pic2.zhimg.com/80/v2-785c4e16f825535b0a494b8688926291_hd.jpg)

Spark Streaming支持多种类型数据源

####  Spark Streaming基础概念

DStream Discretized Stream是SS的基础抽象，代表持续性的数据流和经过各种Spark原语操作后的结果数据流。DStream本质上是一个以时间为键，RDD为值的哈希表，保存了按时间顺序产生的RDD，而每个RDD封装了批处理时间间隔内获取到的数据。SS每次将新产生的RDD添加到哈希表中，而对于已经不再需要的RDD则会从这个哈希表中删除，所以DStream也可以简单地理解为以时间为键的RDD的动态序列。如下图：

![img](https://pic4.zhimg.com/80/v2-964547a7ac168d23ec93962e0f90393f_hd.jpg)

#### 窗口时间间隔

窗口时间间隔又称为窗口长度，它是一个抽象的时间概念，决定了SS对RDD序列进行处理的范围与粒度，即用户可以通过设置窗口长度来对一定时间范围内的数据进行统计和分析。假如设置批处理时间间隔为1s，窗口时间间隔为3s。如下图，DStream每1s会产生一个RDD，红色边框的矩形框就表示窗口时间间隔，一个窗口时间间隔内最多有3个RDD，Spark Streaming在一个窗口时间间隔内最多会对3个RDD中的数据进行统计和分析。

![img](https://pic2.zhimg.com/80/v2-4e71f9176835597116c96dc782255335_hd.jpg)

#### 滑动时间间隔

滑动时间间隔决定了SS程序对数据进行统计和分析的频率。它指的是经过多长时间窗口滑动一次形成新的窗口，滑动时间间隔默认情况下和批处理时间间隔相同，而窗口时间间隔一般设置的要比它们两个大。在这里必须注意的一点是滑动时间间隔和窗口时间间隔的大小一定得设置为批处理时间间隔的整数倍。

如下图，批处理时间间隔是1个时间单位，窗口时间间隔是3个时间单位，滑动时间间隔是2个时间单位。对于初始的窗口time 1-time 3，只有窗口时间间隔满足了才触发数据的处理。这里需要注意的一点是，初始的窗口有可能覆盖的数据没有3个时间单位，但是随着时间的推进，窗口最终会覆盖到3个时间单位的数据。当每个2个时间单位，窗口滑动一次后，会有新的数据流入窗口，这时窗口会移去最早的两个时间单位的数据，而与最新的两个时间单位的数据进行汇总形成新的窗口（time3-time5）。

![img](https://pic3.zhimg.com/80/v2-4227e3366c32072290890e1a959ab36a_hd.jpg)

#### Spark Streaming读取kafka数据

Spark Streaming 与Kafka集成接收数据的方式有两种：

\1. Receiver-based Approach

\2. Direct Approach (No Receivers)

Receiver-based Approach

这个方法使用了Receivers来接收数据。Receivers的实现使用到Kafka高级消费者API。对于所有的Receivers，接收到的数据将会保存在Spark executors中，然后由SS启动的Job来处理这些数据。

然而，在默认的配置下，这种方法在失败的情况下会丢失数据，为了保证零数据丢失，你可以在SS中使用WAL日志，这是在Spark 1.2.0才引入的功能，这使得我们可以将接收到的数据保存到WAL中（WAL日志可以存储在HDFS上），所以在失败的时候，我们可以从WAL中恢复，而不至于丢失数据。架构图如下：

![img](https://pic4.zhimg.com/80/v2-37f32062beda1c9baee837f6b2ee8b6b_hd.jpg)

**需要注意的几点：**

1) kafka的topic和partition并不和SS生成的RDD的partition相对应，所以上面代码中topicMap里增加threads只能增加使用一个receiver消费这个topic的线程数，它并不能增加Spark处理数据的并行数，因为每个input DStream在一个worker机器上只创建一个接受单个数据流的receiver。

2) 可以为不同topic和group创建多个DStream来使用多个receiver并行的接受数据。例如：一个单独的kafka input DStream接受两个topic的数据可以分为两个kafka input DStream，每个只接受一个topic的数据，这样可以并行的接受速度从而提高整体吞吐量。

3) 如果开启了wal来保证数据不丢失话，需要设置checkpoint目录，并且像上面代码一样指定数据序列化到hdfs上的方式（比如：StorageLevel.MEMORY_AND_DISK_SER）

4) 建议每个批处理时间间隔周期接受到的数据最好不要超过接受Executor的内存(Storage)的一半。

要描述清楚 Receiver-based Approach ，我们需要了解其接收流程，分析其内存使用，以及相关参数配置对内存的影响。

#### 数据接收流程

当执行SS的start方法后，SS会标记StreamingContext为Active状态，并且单独起个线程通过ReceiverTracker将从ReceiverInputDStreams中获取的receivers以并行集合的方式分发到worker节点，并运行他们。worker节点会启动ReceiverSupervisor。接着按如下步骤处理：

1) ReceiverSupervisor会启动对应的Receiver(这里是KafkaReceiver)

2) KafkaReceiver 会根据配置启动新的线程接受数据，在该线程中调用ReceiverSupervisor.pushSingle方法填充数据，注意，这里是一条一条填充的。

3) ReceiverSupervisor 会调用 BlockGenerator.addData 进行数据填充。

到目前为止，整个过程不会有太多内存消耗，正常的一个线性调用。所有复杂的数据结构都隐含在BlockGenerator中。

---

###  [Spark Streaming如何应对 Exactly once 语义](https://zybuluo.com/marlin/note/486917)

首先我们梳理Exactly once所涉及到的相关内容，并明确一个讨论基础： 

1. 将Spark计算引擎看作是一个整体 
2. Spark Streaming（以下简写SS）需要对接上下游 
3. Exactly once语义（以下简写EO，或EO语义）涉及到 

\- 上游是否EO到SS 
\- SS作为整体是否保证了EO 
\- SS是否将数据EO地写出到了下游 

4. 只有保证了上述三个过程的EO，才能保证SS是能够实现EO的

#### Spark Streaming上游对接kafka时如何保证Exactly Once？

详情参考[这里](http://spark.apache.org/docs/latest/streaming-kafka-integration.html) 
简要说一下，Spark Streaming使用Direct模式对接上游kafka。无论kafka有多少个partition，使用Direct模式总能保证SS中有相同数量的partition与之相对，也就是说SS中的KafkaRDD的并发数量在Direct模式下是由上游kafka决定的。在这个模式下，kafka的offset是作为KafkaRDD的一部分存在，会存储在checkpoints中，由于checkpoints只存储offset内容，而不存储数据，这就使得checkpoints是相对轻的操作。这就使得SS在遇到故障时，可以从checkpoint中恢复上游kafka的offset，从而保证exactly once。

>  kafka消费者在会保存其消费的进度，也就是offset，存储的位置根据选用的kafka api不同而不同。

#### Spark Streaming输出下游如何保证Exactly once？

详情参考[这里](http://spark.apache.org/docs/latest/streaming-programming-guide.html#semantics-of-output-operations) 
首先输出操作是具有At-least Once语义的，也就是说SS可以保证需要输出的数据一定会输出出去，只不过由于失败等原因可能会输出多次。

那么如何保证Exactly once？

第一种“鸵鸟做法”，就是期望下游（数据）具有幂等特性。

>如果多次写入会产生相同的结果数据，我们可以认为这类写入操作是幂等的。saveAsTextFile 就是一种典型的幂等写入。如果消息中包含唯一主键，那么多次写入相同的数据也不会在数据库中产生重复记录。这种方式也就能等价于 Exactly-once 语义了。但需要注意的是，幂等写入只适用于 Map-only 型的计算流程，即没有 Shuffle、Reduce、Repartition 等操作。



第二种使用事务更新，简要代码如下：

```scala
dstream.foreachRDD { (rdd, time) =>  rdd.foreachPartition { partitionIterator =>    val partitionId = TaskContext.get.partitionId()    val uniqueId = generateUniqueId(time.milliseconds, partitionId)    // use this uniqueId to transactionally commit the data in partitionIterator  }}
```

这样保证同一个partition要么一起更新成功，要么一起失败，通过uniqueId来标识这一次的更新，这就要求下游支持事务机制。

#### Spark Streaming 内部如何保证Exactly Once？

SS内部的实现机制是基于RDD模型的，RDD为保证计算过程中数据不丢失使用了checkpoint机制，也就是说其计算逻辑是RDD的变换过程，也就是DAG，可以在计算过程中的任何一个阶段（也就是这个阶段的RDD）上使用checkpoint方法，就可以保证当后续计算失败，可以从这个checkpoint重新算起，使得计算延续下去。当Spark Streaming场景下，其天然会进行batch操作，也就是说kafka过来的数据，每秒（一个固定batch的时间周期）会对当前kafka中的数据产生一个RDD，那么后续计算就是在这个RDD上进行的。只需要在kafkaRDD这个位置合理使用了checkpoint（这一点在前面已经讲过，可以保证）就能保证SS内部的Exactly once。

> 注意一点：SS中没有Tuple级别的ACK，其操作必然是在RDD的某个partition上的，要么全做，要么不做，要么失败，要么成功，都是基于RDD的partition的。

#### 关于Event Time

在Spark最近的release note中没有明确提及event time。应该是对event time的支持力度还是有限的。 
以下是我对event time在SS中可能的使用方式的理解 
由于SS是基于batch rdd来实现的，如果在kafka接入到SS时就已经使用event time对数据进行划分了，也就是说batch rdd的划分方式使用数据本身的时间戳就可以实现对业务时间的支持。

---

### HBase的数据在HDFS上是怎样存储的, 写入数据的流程是怎样的. 为什么HBase适合写多读少业务

[Hbase的存储](https://www.cnblogs.com/kxdblog/p/4330930.html)

#### Hbase在生态系统中的位置

　　HBase位于结构化存储层，Hadoop HDFS为HBase提供了高可靠性的底层存储支持，Hadoop MapReduce为HBase提供了高性能的计算能力，Zookeeper为HBase提供了稳定服务和failover机制。

 

![img](http://static.oschina.net/uploads/img/201201/25091147_R3An.jpg)

#### Hbase存储的逻辑视图

![img](http://up.2cto.com/2013/0509/20130509015754236.jpeg)

**1）行键(RowKey)**

-- 行键是字节数组, 任何字符串都可以作为行键；
-- 表中的行根据行键进行排序，数据按照Row key的字节序(byte order)排序存储；
-- 所有对表的访问都要通过行键 （单个RowKey访问，或RowKey范围访问，或全表扫描) （二级索引）

**2）列族（ColumnFamily）**

-- CF必须在表定义时给出

-- 每个CF可以有一个或多个列成员(ColumnQualifier)，列成员不需要在表定义时给出，新的列族成员可以随后按需、动态加入

-- 数据按CF分开存储，HBase所谓的列式存储就是根据CF分开存储（每个CF对应一个Store），这种设计非常适合于数据分析的情形

**3）时间戳（TimeStamp）**

-- 每个Cell可能又多个版本，它们之间用时间戳区分

**4）单元格（Cell）**

-- Cell 由行键，列族:限定符，时间戳唯一决定，数据全部以字节码形式存储

**5）区域(Region）**

-- HBase自动把表水平（按Row）划分成多个区域(region)，每个region会保存一个表里面某段连续的数据；
-- 每个表一开始只有一个region，随着数据不断插入表，region不断增大，当增大到一个阀值的时候，region就会等分会两个新的region；
-- 当table中的行不断增多，就会有越来越多的region。这样一张完整的表被保存在多个Region 上。

-- HRegion是HBase中分布式存储和负载均衡的最小单元（默认256M）。最小单元表示不同的HRegion可以分布在不同的HRegionServer上。但一个HRegion不会拆分到多个server上。

特点：

无模式：每行都有一个可排序的主键和任意多的列，列可以根据需要动态的增加，同一张表中不同的行可以有截然不同的列；

面向列：面向列（族）的存储和权限控制，列（族）独立检索；
稀疏：空（null）列并不占用存储空间，表可以设计的非常稀疏；

####  **Hbase的存储格式**

　　HBase中的每张表都通过行键按照一定的范围被分割成多个子表（HRegion），默认一个HRegion超过256M就要被分割成两个，由HRegionServer管理，管理哪些HRegion由HMaster分配。

　　HRegionServer存取一个子表时，会创建一个HRegion对象，然后对表的每个**列族**(Column Family)创建一个Store实例，每个Store都会有0个或多个StoreFile与之对应，每个StoreFile都会对应一个HFile， HFile就是实际的存储文件。因此，一个HRegion有多少个列族就有多少个Store。另外，每个HRegion还拥有一个MemStore实例。memStore存储在内存中，StoreFile存储在HDFS上。

　　

![img](http://img.blog.csdn.net/20131226173700718?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd29zaGl3YW54aW4xMDIyMTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

　　Region虽然是分布式存储的最小单元，但并不是存储的最小单元。Region由一个或者多个Store组成，每个store保存一个columns family；每个Store又由一个memStore和0至多个StoreFile组成，StoreFile包含HFile；memStore存储在内存中，StoreFile存储在HDFS上。

 

　　HBase是基于BigTable的面向列的分布式存储系统，其存储设计是基于Memtable / SSTable设计的，主要分为两部分，一部分为内存中的MemStore (Memtable)，另外一部分为磁盘(这里是HDFS)上的HFile (SSTable)。还有就是存储WAL的log，主要实现类为HLog.

　　本质上MemStore就是一个内存里放着一个保存KEY/VALUE的MAP，当MemStore（默认64MB）写满之后，会开始刷磁盘操作。 

HBase存储在HDFS上的主要包含两种文件类型：

　1. HFile， HBase中KeyValue数据的存储格式，HFile是Hadoop的二进制格式文件，实际上StoreFile就是对HFile做了轻量级包装，即StoreFile底层就是HFile

　　2. HLog File，HBase中WAL（Write Ahead Log） 的存储格式，物理上是Hadoop的Sequence File

**HFile结构：**

Data Block：保存表中的数据，这部分可以被压缩

Meta Block：（可选）保存用户自定义的kv对，可以被压缩。

File Info ：Hfile的meta元信息，不被压缩，定长。

Data Block Index ：Data Block的索引。每个Data块的起始点。

Meta Block Index：（可选的）Meta Block的索引，Meta块的起始点。

Trailer： 定长。保存了每一段的偏移量，读取一个HFile时，会首先读取Trailer，Trailer有指针指向其他数据块的起始点，保存了每个段的起始位置(段的Magic Number用来做安全check)，然后，DataBlock Index会被读取到内存中，这样，当检索某个key时，不需要扫描整个HFile，而只需从内存中找到key所在的block，通过一次磁盘io将整个block读取到内存中，再找到需要的key。DataBlock Index采用LRU机制淘汰。

HFile的Data Block，Meta Block通常采用压缩方式存储。Data Block是HBase I/O的基本单元，为了提高效率，HRegionServer中有基于LRU的Block Cache机制。每个Data块的大小可以在创建一个Table的时候通过参数指定，大号的Block有利于顺序Scan，小号Block利于随机查询。每个Data块除了开头的Magic以外就是一个个KeyValue对拼接而成, Magic内容就是一些随机数字，目的是防止数据损坏。

 

**HFile中的Key-Value结构**

HFile中的每个Key-Value对就是一个简单的byte数组。但这个byte数组包含了很多项信息，并含有固定的结构。（有点类似数据流）

![HFile中的Key-Value](http://www.linuxidc.com/upload/2012_07/120707112147242.jpg)

　　开始是两个长度固定的数值，分别表示Key的长度和Value的长度。紧接着是Key，开始是固定长度的数值，表示RowKey的长度，紧接着是RowKey，然后是固定长度的数值，表示Family的长度，然后是Family（列族），接着是Qualifier（小列），然后是两个固定长度的数值，表示Time Stamp和Key Type（Put/Delete）。Value部分则相对简单，是纯粹的二进制数据。

　　HBase 为每个值维护了多级索引，即：<key, column family, column name（qualifer）, timestamp>

#### **Hbase写数据流程**

a)  Client发起了一个HTable.put(Put)请求给HRegionServer

b)  HRegionServer会将请求匹配到某个具体的HRegion上面

c)  决定是否写WAL log。WAL log文件是一个标准的Hadoop SequenceFile，文件中存储了HLogKey，这些Keys包含了和实际数据对应的序列号，主要用于崩溃恢复。

d) Put数据保存到MemStore中，同时检查MemStore状态，如果满了，则触发Flush to Disk请求。

e)  HRegionServer处理Flush to Disk的请求，将数据写成HFile文件并存到HDFS上，并且存储最后写入的数据序列号，这样就可以知道哪些数据已经存入了永久存储的HDFS中。

由于不同的列族会共享region，所以有可能出现，一个列族已经有1000万行，而另外一个才100行。当一个要求region分割的时候，会导致100行的列会同样分布到多个region中。所以，一般建议不要设置多个列族。

 

#### **Hbase快速响应数据**

　　hbase上的数据是以storefile(HFile)二进制流的形式存储在HDFS上block块中；但是HDFS并不知道的hbase存的是什么，它只把存储文件是为二进制文件，也就是说，hbase的存储数据对于HDFS文件系统是透明的。  

　　HBase HRegion servers集群中的所有的region的数据在服务器启动时都是被打开的，并且在内冲初始化一些memstore，相应的这就在一定程度上加快系统响应；而Hadoop中的block中的数据文件默认是关闭的，只有在需要的时候才打开，处理完数据后就关闭，这在一定程度上就增加了响应时间。 
     从根本上说，HBase能提供实时计算服务主要原因是由其架构和底层的数据结构决定的，即由**LSM-Tree** + **HTable(region分区)** + Cache决定——客户端可以直接定位到要查数据所在的HRegion server服务器，然后直接在服务器的一个region上查找要匹配的数据，并且这些数据部分是经过cache缓存的。

![img](http://img.blog.csdn.net/20131226174043000?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd29zaGl3YW54aW4xMDIyMTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

不同的region会被Master分配给相应的RegionServer进行管理：

HBase中有两张特殊的Table，-ROOT-和.META.

.META.：记录了用户表的Region信息，.META.可以有多个regoin，以及RegionServer的服务器地址。

-ROOT-：记录了.META.表的Region信息，-ROOT-只有一个region

&Oslash; Zookeeper中记录了-ROOT-表的location

 

　　Client访问用户数据之前需要首先访问zookeeper，然后访问-ROOT-表，接着访问.META.表，最后才能找到用户数据的位置去访问，中间需要多次网络操作，不过client端会做cache缓存。


​    1、Client会通过内部缓存的相关的-ROOT-中的信息和.META.中的信息直接连接与请求数据匹配的HRegion server； 
    2、然后直接定位到该服务器上与客户请求对应的region，客户请求首先会查询该region在内存中的缓存——memstore(memstore是是一个按key排序的树形结构的缓冲区)； 
    3、如果在memstore中查到结果则直接将结果返回给client； 
    4、在memstore中没有查到匹配的数据，接下来会读已持久化的storefile文件中的数据。storefile也是按key排序的树形结构的文件——并且是特别为范围查询或block查询优化过的，；另外hbase读取磁盘文件是按其基本I/O单元(即 hbase block)读数据的。具体就是过程就是： 
    如果在BlockCache中能查到要造的数据则这届返回结果，否则就读去相应的storefile文件中读取一block的数据，如果还没有读到要查的数据，就将该数据block放到HRegion Server的blockcache中，然后接着读下一block块儿的数据，一直到这样循环的block数据直到找到要请求的数据并返回结果；如果将该region中的数据都没有查到要找的数据，最后接直接返回null，表示没有找的匹配的数据。当然blockcache会在其大小大于一的阀值（heapsize * hfile.block.cache.size * 0.85）后启动基于LRU算法的淘汰机制，将最老最不常用的block删除。

#### **Hbase容错和恢复**

HLogFile

　　HLog文件就是一个普通的Hadoop Sequence File，Sequence File 的Key是HLogKey对象，HLogKey中记录了写入数据的归属信息，除了table和region名字外，同时还包括 sequence number和timestamp，timestamp是“写入时间”，sequence number的起始值为0，或者是最近一次存入文件系统中sequence number。

　　HLog Sequece File的Value是HBase的KeyValue对象，即对应HFile中的KeyValue.

该机制用于数据的容错和恢复：

　　每个HRegionServer中都有一个HLog对象，HLog是一个实现Write Ahead Log的类，在每次用户操作写入MemStore的同时，也会写一份数据到HLog文件中（HLog文件格式见后续），HLog文件定期会滚动出新的，并删除旧的文件（已持久化到StoreFile中的数据）。当HRegionServer意外终止后，HMaster会通过Zookeeper感知到，HMaster首先会处理遗留的 HLog文件，将其中不同Region的Log数据进行拆分，分别放到相应region的目录下，然后再将失效的region重新分配，领取 到这些region的HRegionServer在Load Region的过程中，会发现有历史HLog需要处理，因此会Replay HLog中的数据到MemStore中，然后flush到StoreFiles，完成数据恢复。

HBase容错性
**Master容错**：Zookeeper重新选择一个新的Master
*无Master过程中，数据读取仍照常进行；
*无master过程中，region切分、负载均衡等无法进行；
**RegionServer容错**：定时向Zookeeper汇报心跳，如果一旦时间内未出现心跳，Master将该RegionServer上的Region重新分配到其他RegionServer上，失效服务器上“预写”日志由主服务器进行分割并派送给新的RegionServer
**Zookeeper容错**：Zookeeper是一个可靠地服务，一般配置3或5个Zookeeper实例

---

### Spark/Hive中大表join小表的优化方法. 

[Spark join问题](https://blog.csdn.net/chenjieit619/article/details/71548953)

#### 二：RDD join 问题 

　　程序RDD 的join问题的一些总结：A表join B表，对B表进行更新问题，都可以把表转换成(k,v)的形式(k可以由多个字段拼接而成，v可以是一个对象，也可以是一个字段)，最终问题转化为A(k,v) join B(k,v) on A.k = B.k 
1、full join 
![这里写图片描述](https://img-blog.csdn.net/20160806104736984) 
使用场景：A表和B表进行full join，并且A表比B表的数据量不在一个数量级，如果在一个数量级，则直接使用full out join算子执行 
方案： 
①把full out join转化为A表left out join B表 
　　②之后进行map操作，根据A对B进行更新 
　　③map操作中用HashSet记录A 和B交集的key 
　　④根据③中得到的HashSet对B进行filter操作，过滤掉已经left out join的记录，然后对B中剩下的记录进行foreach遍历更新 
　　⑤调用TDW接口把RDD分别写入文件中 
代码： 
![这里写图片描述](https://img-blog.csdn.net/20160806104804281) 
2、小表join大表 
使用场景： 
一个小表A和一个大表B的连接操作，小表指文件足够小，可以加载到内存中，该[算法](http://lib.csdn.net/base/datastructure)可以将join算子执行在Map端，无需经历shuffe和reduce等阶段，因此效率很高 
方案： 
　　①把A(k,v)进行广播 
　　②在Map端对B进行过滤和更新 
　　③把更新后的RDD写入文件中 
代码： 
![这里写图片描述](https://img-blog.csdn.net/20160806104826469)
3、大表join大表 
使用场景： 
当两个表数据量非常大，其中任何一个都不能够放到内存中，可以使用Spark的join算子，通过该算子实现reduce-side-join 
概念：RDD依赖关系 
在spark中如何表示RDD之间的依赖关系分为两类： 
①窄依赖：每个父RDD的分区都至多被一个子RDD的分区使用，即为OneToOneDependecies； 
②宽依赖：多个子RDD的分区依赖一个父RDD的分区，即为OneToManyDependecies。 
例如，map操作是一种窄依赖，而join操作是一种宽依赖（除非父RDD已经基于Hash策略被划分过了） 
![这里写图片描述](https://img-blog.csdn.net/20160806104849595) 
　　图：宽依赖和窄依赖 
方案： 
　　　由上图可知，join分为宽依赖和窄依赖，如果RDD有相同的partitioner，那么将不会引起shuffle，因此我们可以对RDD进行Hash分区。分别对A和B用同一个函数进行Partition，比如按照首字母进行Partition，那么A和B都可以分成26个Partition，并且A1只需要和B1进行join，A1不需要和B剩下的25个Partition进行join，这样就大大的减少了join次数，最好的办法是对表进行分区，每次只取两个对应分区的数据进行join操作。具体的Hash Partition函数需要根据具体的应用场景实现，比如：如果key是URL，那么就可以根据域名进行分区。分区大小需要根据task-nums、num-executors以及executor-cores确定。 
![这里写图片描述](https://img-blog.csdn.net/20160806104918001) 
　　　图：Spark中CoGroupedRDD.[Scala](http://lib.csdn.net/base/scala)源码 
代码： 
![这里写图片描述](https://img-blog.csdn.net/20160806104941206) 
图：分区函数 
![这里写图片描述](https://img-blog.csdn.net/20160806104957066) 
图：对RDD进行分区 
4、RDD join引起 shuffle问题 
　　请参考[《Spark性能优化指南》](https://www.baidu.com/s?wd=%E3%80%8ASpark%E6%80%A7%E8%83%BD%E4%BC%98%E5%8C%96%E6%8C%87%E5%8D%97%E3%80%8B&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)系列，里边详细讲解了引起shuffle的原因，以及不通场景的解决办法，强烈推荐。 

#### 三：遇到的问题 

　　　列出我在写Spark程序遇到的两个问题： 
1、RDD的API所引用的所有对象，都必须是可序列化的 
![这里写图片描述](https://img-blog.csdn.net/20160806105038942)
图：RDD中引用对象 
![这里写图片描述](https://img-blog.csdn.net/20160806105047489)
图：报错信息

　　　在RDD的API里所引用的在RDD的API里所引用的所有对象，都必须是可序列化的，因为RDD分布在多台机器是，代码和所引用的对象会序列化，然后复制到多台机器，所以凡是被引用的数据，都必须是可序列化的。否则会报java.lang.NotSerializableException: scala.util.Random 异常，解决办法就是把引用对象序列化 extends Serializable或者使用kryo序列化。 
在一个RDD的api里不可以引用另外一个RDD 
　　SPARK-5063 in spark,Spark does not support nested RDDs or performing Spark actions inside of transformations; this usually leads to NullPointerExceptions (seeSPARK-718 as one example). The confusing NPE is one of the most common sources of Spark questions on StackOverflow: 
　　上边英文大致意思是：Spark的transformation算子中不支持嵌套RDD，会导致空指针，如果其中一个RDD数据量不大，则可以用文章中提高的广播变量解决这个问题，如果数据量很大使用广播变量会导致OOM，那么就要从其他方面进行优化或者从业务逻辑进行出发。

#### 四：总结 

　　这一个月我的主要工作是把Sql转化成Spark程序，以及去不断的去优化，提高效率。在把Sql转化成Spark程序有很多可以优化的点,我选择了其中一个点进行了总结，总结的有不妥的地方，欢迎拍砖，一起交流。 
　　时间过的很快，在这里已经实习一个月了，在导师dezhiliu和小组成员的帮助下 ，自己成长了很多，也学到了很多东西，不仅仅是Spark相关的。特别的感谢我的导师和[我的小伙伴](https://www.baidu.com/s?wd=%E6%88%91%E7%9A%84%E5%B0%8F%E4%BC%99%E4%BC%B4&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)们。

### [spark十亿数据join优化](https://daizuozhuo.github.io/spark-join/)

最近在项目中用Spark join了几十亿的数据，在debug和不断优化性能中感觉收获良多，特此记录一下。

任务很简单，就是join两张表，表A ship有几千万行，包含每日寄出去的包裹的信息，表B item有几十亿行，包括所有商品的属性，我们需要把商品的属性信息加到每个包裹里面的商品上。

一开始我就是把它当成一个很简单的任务，不就是一个简单的left join吗？于是写下了如下代码：

```java
Dataset<Row> shipItems = getSpark().sql("select " +
        "ship.*, item.* " +
        "from ship left join item " +
        "on ship.asin = item.item_asin")
        .drop("item_asin");
shipItems.createOrReplaceTempView("ship_items");
```

但是一经实践就遇到一下错误：

```java
17/07/10 02:26:14 ERROR YarnClientSchedulerBackend: Yarn application has already exited with state KILLED!
17/07/10 02:26:14 INFO SparkUI: Stopped Spark web UI at http://172.31.5.203:4040
17/07/10 02:26:14 ERROR TransportClient: Failed to send RPC 8654033690236908099 to /172.31.5.177:42830: java.nio.channels.ClosedChannelException
java.nio.channels.ClosedChannelException
        at io.netty.channel.AbstractChannel$AbstractUnsafe.write(...)(Unknown Source)
17/07/10 02:26:14 ERROR YarnSchedulerBackend$YarnSchedulerEndpoint: Sending RequestExecutors(0,0,Map()) to AM was unsuccessful
java.io.IOException: Failed to send RPC 8654033690236908099 to /172.31.5.177:42830: java.nio.channels.ClosedChannelException
```

这个错误表明spark的任务在Yarn的executor中被kill了，然后在看一下Yarn executor中的log :”WARN: Received singal SIG_TERM”, 果然因为内存使用太多被Yarn kill了。

这是为什么呢？来估算一下完成这个任务需要多少计算资源吧。假设每个表大概有50字段，每个字段占用10个字符，那么当Spark将表A load进内存时需要50*10*10^7/10^9 = 5G,而将表B load进内存时需要：50*10*10^9/1000^9 = 500G。但是当我把整个集群的内存加到600G的时候这个错误仍然没有解决。猜测内存600G的集群仍然无法join表A和表B可能是因为在join过程中可能生成了多份数据而超过了表A和表B本来的大小，这个时候如果再继续提高集群大小就有点不划算了，所以我们开始考虑如何对程序进行优化。

此时该程序所需内存如下所示： 

![original join](https://daizuozhuo.github.io/images/spark-join-1.png)其中深蓝色代表已经存在于集群中内存中的数据，浅蓝色代表正在生成中的数据，那么此时整个集群所需要的内存为两倍于表A和表B大小：(500 + 5) * 2 ＝ 2010 G. ???

因为我们表A left join 表B 之后的结果和表A的大小是一样的，所以实际上大部分表B的数据是没有用的，那么我们可不可以先将一部分表B的数据去掉呢？我们可以确定的是只有存在于表A中物品才会出现在结果中，所以我们可以将表A中的所有商品ID取出来做成一个集合，这个集合的大小为 20 * 10^7/1000/1000 = 200 M，然后将这个集合broadcast到每一个slave节点进行filter，这样可以得到一个大大缩减版的表B。更重要的是，我们得到缩减版的表B之后，原来那个巨大的表B就可以从内存中删除了，这样可以大大减少内存的使用，最终使得程序成功运行。优化之后的代码如下：

```java
Dataset<Row> distinctAsin = getSpark()
        .sql("select distinct asin from ship")
        .persist(StorageLevel.DISK_ONLY())
//only keep items appeared in shipments
Dataset<Row> filteredItems = getSpark()
        .sql("select * from item")
        .withColumnRenamed("asin", "item_asin")
        .join(functions.broadcast(distinctAsin),
                functions.col("item_asin").equalTo(functions.c("asin")), "leftsemi")
        .persist(StorageLevel.DISK_ONLY());
filteredItems.createOrReplaceTempView("filter_item");
Dataset<Row> shipItems = getSpark().sql("select " +
        "ship.*, filter_item.* " +
        "from ship left join filter_item " +
        "on ship.asin = filter_item.item_asin")
        .drop("item_asin");
shipItems.createOrReplaceTempView("ship_items");
```

此时该程序所需的内存如下： ![optimized join](https://daizuozhuo.github.io/images/spark-join-2.png)其中深蓝色代表已经存在于集群中内存中的数据，浅蓝色代表正在生成中的数据，蓝色阴影代表已经处理过从内存中删除的数据，那么此时整个集群所需要的内存为两倍于表A和表C大小：(5 + 5) * 2 ＝ 20 G.

![memory usage](https://daizuozhuo.github.io/images/spark-join-ganglia.png)通过gangalia监控到的集群内存使用量.

---

### [hive数据倾斜及优化总结](https://www.zybuluo.com/hadoopMan/note/238429)

#### 0，严格模式

对分区表进行查询，在where子语句中没有加分区过滤的话，将禁止提交任务(默认：nostrict)。 
严格模式设置方法：

```
set hive.mapred.mode=strict;
```

注意：使用严格模式将禁止3种类型的查询： 
(1).对于分区表，不加分区字段过滤条件不能执行 
(2).对于order by语句，必须使用limit语句。 
(3).限制笛卡尔积的查询(join的时候不使用on，而使用where的).

[Hive严格模式](https://blog.csdn.net/lzm1340458776/article/details/43233639)

Hive提供了一个严格模式，可以防止用户执行那些可能产生意向不到的不好的效果的查询。说通俗一点就是这种模式可以阻止某些查询的执行。通过如下语句设置严格模式：

```
hive> set hive.mapred.mode=strict;
```

**设置为严格模式后，可以禁止3种类型的查询：**

**(1)：带有分区的表的查询**

如果在一个分区表执行hive，除非where语句中包含分区字段过滤条件来显示数据范围，否则不允许执行。换句话说就是在严格模式下不允许用户扫描所有的分区。进行这个限制的原因是，通常分区表都拥有非常大的数据集，而且数据增加迅速。如果不进行分区限制的查询会消耗巨大的资源来处理，如下不带分区的查询语句：

```sql
hive> SELECT DISTINCT(planner_id) FROM fracture_ins WHERE planner_id=5;
```

执行后会出现如下错误：

```sql
FAILED: Error in semantic analysis: No Partition Predicate Found for Alias "fracture_ins" Table "fracture_ins
```

解决方案是在where中增加分区条件：

```sql
hive> SELECT DISTINCT(planner_id) FROM fracture_ins
       > WHERE planner_id=5 AND hit_date=20120101;
```

 

**(2)：带有order by的查询**

对于使用了order by的查询，要求必须有limit语句。因为order by为了执行排序过程会将所有的结果分发到同一个reduce中进行处理，强制要求用户增加这个limit语句可以防止reduce额外消耗资源，如下是不带limit关键字的查询语句：

```sql
hive> SELECT * FROM fracture_ins WHERE hit_date>2012 ORDER BY planner_id;
```

出现如下错误：

```sql
FAILED: Error in semantic analysis: line 1:56 In strict mode,
limit must be specified if ORDER BY is present planner_id
```

解决方案就是增加一个limit关键字：

```sql
hive> SELECT * FROM fracture_ins WHERE hit_date>2012 ORDER BY planner_id
        > LIMIT 100000;
```

 **(3)：限制笛卡尔积的查询**

对关系型数据库非常了解的用户可能期望在执行join查询的时候不适用on语句而是使用where语句，这样关系型数据库的执行优化器就可以高效的将where语句转换成那个on语句了。不幸的是，Hive并不支持这样的优化，因为如果表非常大的话，就会出现不可控的情况，如下是不带on的语句：

```sql
hive> SELECT * FROM fracture_act JOIN fracture_ads
> WHERE fracture_act.planner_id = fracture_ads.planner_id;
```

出现如下错误：

```sql
FAILED: Error in semantic analysis: In strict mode, cartesian product
is not allowed. If you really want to perform the operation,
+set hive.mapred.mode=nonstrict+
```

 

解决方案就是加上on语句：

```sql
hive> SELECT * FROM fracture_act JOIN fracture_ads
        > ON (fracture_act.planner_id = fracture_ads.planner_id);
```

####  1. hive中桶的概述

对于每一个表（table）或者分区， Hive可以进一步组织成桶，也就是说桶是更为细粒度的数据范围划分。Hive也是 针对某一列进行桶的组织。Hive采用对列值哈希，然后除以桶的个数求余的方式决定该条记录存放在哪个桶当中。 
把表（或者分区）组织成桶（Bucket）有两个理由：

（1）获得更高的查询处理效率。桶为表加上了额外的结构，Hive 在处理有些查询时能利用这个结构。具体而言，连接两个在（包含连接列的）相同列上划分了桶的表，可以使用 Map 端连接 （Map-side join）高效的实现。比如JOIN操作。对于JOIN操作两个表有一个相同的列，如果对这两个表都进行了桶操作。那么将保存相同列值的桶进行JOIN操作就可以，可以大大较少JOIN的数据量。

（2）使取样（sampling）更高效。在处理大规模数据集时，在开发和修改查询的阶段，如果能在数据集的一小部分数据上试运行查询，会带来很多方便。 

1. 创建带桶的 table

```sql
create table bucketed_user(id int,name string) clustered by (id) sorted by(name) into 4 buckets row format delimited fields terminated by '\t' stored as textfile; 
```

首先，我们来看如何告诉Hive—个表应该被划分成桶。我们使用CLUSTERED BY 子句来指定划分桶所用的列和要划分的桶的个数：

```sql
CREATE TABLE bucketed_user (id INT) name STRING) CLUSTERED BY (id) INTO 4 BUCKETS; 
```

在这里，我们使用用户ID来确定如何划分桶(Hive使用对值进行哈希并将结果除 以桶的个数取余数。这样，任何一桶里都会有一个随机的用户集合（PS：其实也能说是随机，不是吗？）。

对于map端连接的情况，两个表以相同方式划分桶。处理左边表内某个桶的 mapper知道右边表内相匹配的行在对应的桶内。因此，mapper只需要获取那个桶 (这只是右边表内存储数据的一小部分)即可进行连接。这一优化方法并不一定要求 两个表必须桶的个数相同，两个表的桶个数是倍数关系也可以。用HiveQL对两个划分了桶的表进行连接，可参见“map连接”部分（P400）。

桶中的数据可以根据一个或多个列另外进行排序。由于这样对每个桶的连接变成了高效的归并排序(merge-sort), 因此可以进一步提升map端连接的效率。以下语法声明一个表使其使用排序桶：

```sql
CREATE TABLE bucketed_users (id INT, name STRING) CLUSTERED BY (id) SORTED BY (id ASC) INTO 4 BUCKETS; 
```

我们如何保证表中的数据都划分成桶了呢？把在Hive外生成的数据加载到划分成 桶的表中，当然是可以的。其实让Hive来划分桶更容易。这一操作通常针对已有的表。

Hive并不检查数据文件中的桶是否和表定义中的桶一致(无论是对于桶 的数量或用于划分桶的列）。如果两者不匹配，在査询时可能会碰到错 误或未定义的结果。因此，建议让Hive来进行划分桶的操作。

#### 2.hive中join优化

##### 2.1 mapside join

方法一： 
老版本

```sql
select /*+ MAPJOIN(time_dim) */ count(*) from store_sales join time_dim on (ss_sold_time_sk = t_time_sk)s
```

方法二： 
这个可以由hive自动进行map端join

```sql
set hive.auto.convert.join=true;
select count(*) fromstore_sales join time_dim on (ss_sold_time_sk = t_time_sk)
```

##### 2.2，执行下面这段代码将会产生两个map-only方法：

```sql
select /*+ MAPJOIN(time_dim, date_dim) */ count(*) fromstore_sales join time_dim on (ss_sold_time_sk = t_time_sk) join date_dim on (ss_sold_date_sk = d_date_sk)where t_hour = 8 and d_year = 2002
```

设置下面两个属性hive将会进行自动执行上述过程，第一个属性默认为true，第二个属性是设置map端join适合读取内存文件的大小。

```sql
set hive.auto.convert.join.noconditionaltask = true;
set hive.auto.convert.join.noconditionaltask.size = 10000000;
```

##### 2.3，Sort-Merge-Bucket (SMB) joins可以被转化为SMB map joins。我们只需要设置一下几个参数即可：

```sql
set hive.auto.convert.sortmerge.join=true;
set hive.optimize.bucketmapjoin = true;
set hive.optimize.bucketmapjoin.sortedmerge = true;
```

##### 2.4，也可已设置大表选择的策略,使用下面属性：

```sql
set hive.auto.convert.sortmerge.join.bigtable.selection.policy     = org.apache.hadoop.hive.ql.optimizer.TableSizeBasedBigTableSelectorForAutoSMJ;
```

总共有以下几个策略可提供设置：

```sql
org.apache.hadoop.hive.ql.optimizer.AvgPartitionSizeBasedBigTableSelectorForAutoSMJ (default)
org.apache.hadoop.hive.ql.optimizer.LeftmostBigTableSelectorForAutoSMJ
org.apache.hadoop.hive.ql.optimizer.TableSizeBasedBigTableSelectorForAutoSMJ
```

详细请参考一下连接： 
[hive中进行连接方案详解](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Joins)

#### 3，数据倾斜

Hive的执行是分阶段的，map处理数据量的差异取决于上一个stage的reduce输出，所以如何将数据均匀的分配到各个reduce中，就是解决数据倾斜的根本所在。

##### 3.1 操作

![数据倾斜的原因.png-12.7kB](http://static.zybuluo.com/hadoopMan/g6r6z835fbxir5l8cktco411/%E6%95%B0%E6%8D%AE%E5%80%BE%E6%96%9C%E7%9A%84%E5%8E%9F%E5%9B%A0.png)

##### 3.2原因

1，数据在节点上分布不均 
2，key分布不均(key中存在个别值数据量比较大，比如NULL,那么join时就会容易发生数据倾斜). 
3，count(disctinct key)，在数据量比较大的时候容易发生数据倾斜，因为count（distinct）是按照group by字段进行分组的。 
4，group by的使用容易造成数据倾斜。 
5，业务数据本身的特性 
6，建表时考虑不周 
7，某些SQL语句本身就有数据倾斜

##### 3.3 表现

任务进度长时间维持在99%左右，查看任务监控页面发现只有少量reduce任务未完成。因为其处理的数据量和其他reduce差异过大。单一reduce的记录数与平均记录数差异过大，通常可能达到3倍甚至更多。最长时长远大于平均时长。

#### 4，数据倾斜的解决方案

##### 4.1 参数调节

```
set hive.map.aggr=true;Map 端部分聚合，相当于Combiner
set hive.groupby.skewindata=true;
```

有数据倾斜的时候进行负载均衡，当选项设定为 true，生成的查询计划会有两个 MR Job。第一个 MR Job 中，Map 的输出结果集合会随机分布到 Reduce 中，每个 Reduce 做部分聚合操作，并输出结果，这样处理的结果是相同的 Group By Key 有可能被分发到不同的 Reduce 中，从而达到负载均衡的目的；第二个 MR Job 再根据预处理的数据结果按照 Group By Key 分布到 Reduce 中（这个过程可以保证相同的 Group By Key 被分布到同一个 Reduce 中），最后完成最终的聚合操作。 
![负载均衡.png-44.8kB](http://static.zybuluo.com/hadoopMan/1iz6ck5172xvd78u5vuxipkn/%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1.png)

##### 4.2 SQL语句调节：

###### 如何Join：

关于驱动表的选取，选用join key分布最均匀的表作为驱动表 
做好列裁剪和filter操作，以达到两表做join的时候，数据量相对变小的效果。

###### 大小表Join：

使用map join让小的维度表（1000条以下的记录条数） 先进内存。在map端完成reduce.

###### 大表Join大表：

把空值的key变成一个字符串加上随机数，把倾斜的数据分到不同的reduce上，由于null值关联不上，处理后并不影响最终结果。 
count distinct大量相同特殊值 
容易倾斜，当xx字段存在大量的某个值时，NULL或者空的记录

> > > 解决思路 
> > > 将特定的值，进行特定的处理。 
> > > 比如是null， 
> > > \* 过滤掉，where case 
> > > \* 特定方式转换特定的值，使得这些值不一样，同时这些值不影响分析。

###### group by维度过小：

Group by 
在Map端进行部分数据合并

> > set hive.map.aggr ; --> 是否在Map端进行数据聚合，默认设置为true； 
> > set hive.groupby.mapaggr.checkinterval ; --> 在Map端进行聚合操作的条目数。 
> > 进行负载均衡负载均衡 
> > set hive.groupby.skewindata ; 
> > 默认值是false，需要设置成true ; 
> > 当设置为true时，会变成两个MapReduce ;
> >
> > > \> 
> > > 第一个MR JOb中，map的输出结果会随机分布到Reduce中，每个Reduce做部分聚合操作，并输出结果，这样出来的结果相同的Group By Key有可能被分发到不同的Reduce中，从而达到辅助均衡目的。 
> > > \> 
> > > 第二个MR JOb，会根据预处理数据结果按照key分布到Reduce中，最终完成聚合操作。

网络参考： 
采用sum() group by的方式来替换count(distinct)完成计算。 
特殊情况特殊处理： 
在业务逻辑优化效果的不大情况下，有些时候是可以将倾斜的数据单独拿出来处理。最后union回去。

#### 5，典型的业务场景

##### 5.1 空值产生的数据倾斜

场景：如日志中，常会有信息丢失的问题，比如日志中的 user_id，如果取其中的 user_id 和 用户表中的user_id 关联，会碰到数据倾斜的问题。 
解决方法1： user_id为空的不参与关联（红色字体为修改后）

```
select * from log a  join users b  on a.user_id is not null  and a.user_id = b.user_idunion allselect * from log a  where a.user_id is null;
```

解决方法2 ：赋与空值分新的key值

```
select *  from log a  left outer join users b  on case when a.user_id is null then concat(‘hive’,rand() ) else a.user_id end = b.user_id;
```

结论：方法2比方法1效率更好，不但io少了，而且作业数也少了。解决方法1中 log读取两次，jobs是2。解决方法2 job数是1 。这个优化适合无效 id (比如 -99 , ’’, null 等) 产生的倾斜问题。把空值的 key 变成一个字符串加上随机数，就能把倾斜的数据分到不同的reduce上 ,解决数据倾斜问题。

##### 5.2 不同数据类型关联产生数据倾斜

场景：用户表中user_id字段为int，log表中user_id字段既有string类型也有int类型。当按照user_id进行两个表的Join操作时，默认的Hash操作会按int型的id来进行分配，这样会导致所有string类型id的记录都分配到一个Reducer中。 
解决方法：把数字类型转换成字符串类型

```sql
select * from users a  left outer join logs b  on a.usr_id = cast(b.user_id as string)
```

##### 5.3 小表不小不大，怎么用 map join 解决倾斜问题

使用 map join解决小表(记录数少)关联大表的数据倾斜问题，这个方法使用的频率非常高，但如果小表很大，大到map join会出现bug或异常，这时就需要特别的处理。 以下例子:

```sql
select * from log a  left outer join users b  on a.user_id = b.user_id;
```

users 表有 600w+ 的记录，把 users 分发到所有的 map 上也是个不小的开销，而且 map join 不支持这么大的小表。如果用普通的 join，又会碰到数据倾斜的问题。 
解决方法：

```sql
select /*+mapjoin(x)*/* from log a  left outer join (    select  /*+mapjoin(c)*/d.*      from ( select distinct user_id from log ) c      join users d      on c.user_id = d.user_id    ) x  on a.user_id = b.user_id;
```

假如，log里user_id有上百万个，这就又回到原来map join问题。所幸，每日的会员uv不会太多，有交易的会员不会太多，有点击的会员不会太多，有佣金的会员不会太多等等。所以这个方法能解决很多场景下的数据倾斜问题。

#### 6，数据倾斜总结

使map的输出数据更均匀的分布到reduce中去，是我们的最终目标。由于Hash算法的局限性，按key Hash会或多或少的造成数据倾斜。大量经验表明数据倾斜的原因是人为的建表疏忽或业务逻辑可以规避的。在此给出较为通用的步骤： 
1、采样log表，哪些user_id比较倾斜，得到一个结果表tmp1。由于对计算框架来说，所有的数据过来，他都是不知道数据分布情况的，所以采样是并不可少的。 
2、数据的分布符合社会学统计规则，贫富不均。倾斜的key不会太多，就像一个社会的富人不多，奇特的人不多一样。所以tmp1记录数会很少。把tmp1和users做map join生成tmp2,把tmp2读到distribute file cache。这是一个map过程。 
3、map读入users和log，假如记录来自log,则检查user_id是否在tmp2里，如果是，输出到本地文件a,否则生成的key,value对，假如记录来自member,生成的key,value对，进入reduce阶段。 
4、最终把a文件，把Stage3 reduce阶段输出的文件合并起写到hdfs。

如果确认业务需要这样倾斜的逻辑，考虑以下的优化方案： 
1、对于join，在判断小表不大于1G的情况下，使用map join 
2、对于group by或distinct，设定

```
hive.groupby.skewindata=true
```

3、尽量使用上述的SQL语句调节进行优化 











---

## 数据仓库设计

```
1. 之前业务中的数据仓库是如何分层的, 怎样建模, 主题如何划分
2. 从ODS到DW层的ETL, 做了哪些工作
3. 1~3NF的含义. 维度建模中星型模型和雪花模型的不同. 代理键是什么, 支架表是什么
4. 如何处理缓慢变化维. 怎样建设拉链表, 如何在拉链表中恢复最新数据
```

## 算法和应用题

```
1. 最长公共子序列(LCS)问题. 动态规划
2. 找出二叉树中任意两个节点的最低公共根节点, 如果树是BST呢. 深度优先搜索+二分查找树性质
3. 10亿条64B长的URL, 限定1G内存, 做计数, 如果要TopN的话呢. 哈希分桶+堆排序时间复杂度
4. 用户行为日志有UID和时间戳, 设定一个session间隔. 离线及在线地计算用户的平均session长度
5. 之前做过标签推荐系统, 详细讲一下架构和自己设计的算法. 算法流程是行为评分+指数衰减+线性归一化+余弦相似度/皮尔逊相关性+TF-IDF打压

```

# 头条
作者：Commando20180403011197

链接：https://www.nowcoder.com/discuss/142963?type=2&order=0&pos=5&page=1

来源：牛客网

* 面的公司并不多。拿到了头条和阿里的意向书，如果不出意外应该就是这俩选一个了。
没有实习，没有项目经历，什么都没有，上去就是硬怼。本科不是相关专业，研究生转方向但是是留学生，也不是特别厉害的学校。各方面都比较劣势。
优势就是特别能背。

## 一面

```
面岗位相关的基础，上来面试官直接发现我什么经历都没有，我直接说熟悉hadoop。挑了HDFS让我讲一下。
NN和DN。
HA的实现
zookeeper的原理，zk是如何保证一致性的，zk是如何判断session超时，connection超时的。如何触发回调。
client和HDFS文件的读写过程，延迟太高，怎么解决。我当时脑子一蒙，直接说了设计的就是高吞吐量的文件系统。
yarn的结构，RM和NM的交互，如何分配任务的。
yarn在什么层面调度，和k8s和mesos有什么区别，内存调度是什么怎么调度的，如果考虑CPU怎么调度的。如何实现隔离的，Control group 和Namespace是怎么回事。
手撕算法，链表排序。
人生中的第一次面试。。。。面的一团糟，多亏面试官使劲高抬贵手。。。
```

### zookeeper的原理，zk是如何保证一致性的，zk是如何判断session超时，connection超时的。如何触发回调。

[ZooKeeper原理与应用](https://www.jianshu.com/p/84ad63127cd1)

 ZooKeeper是一个典型的**分布式数据一致性**的**解决方案**。分布式应用程序可以基于它实现诸如**数据发布/订阅、负载均衡、命名服务、分布式协调/通知、集群管理、Master选举、分布式锁和分布式队列**等功能。ZooKeeper可以保证如下分布式一致性特性。

- **顺序一致性**
   从同一个客户端发起的事务请求，最终将会严格按照其**发起顺序**被应用到ZooKeeper中。
- **原子性**
   所有事务请求的结果在集群中**所有机器**上的应用情况是一致的，也就是说要么整个集群所有集群**都成功**应用了某一个事务，要么**都没有**应用，一定**不会出现**集群中部分机器应用了该事务，而另外一部分没有应用的情况。
- 单一视图
   **无论**客户端连接的是**哪个**ZooKeeper服务器，其看到的**服务端数据模型**都是**一致**的。
- **可靠性**
   一旦服务端成功地应用了一个事务，并完成对客户端的响应，那么该事务所引起的服务端状态变更将会**被一直保留**下来，除非有另一个事务又对其进行了变更。
- **实时性**
   通常人们看到实时性的第一反应是，一旦一个事务被成功应用，那么客户端能够立即从服务端上读取到这个事务变更后的最新数据状态。这里需要注意的是，ZooKeeper**仅仅保证**在**一定的时间段内**，客户端**最终**一定能够从服务端上读取到**最新**的数据状态。

 

 

 

 

 









## 二面

```
面的计算机基础：
上来手撕，剑指原题，两个栈实现一个队列。
问了一下转专业的经历。
讲个简历的项目。
操作系统中的进程和线程的区别。
JVM的内存模型说一下，堆，栈，永久区，GC。
Java的多线程。
Hadoop的MR和Spark有什么区别，为什么Spark有优势。
TCP
用过什么数据库啊，然后就没往深了问了。
手推神经网络上的反向传播，把链式求导写出来，激活函数sigmoid。
介绍一下LSTM，和GRU的区别。
过。
```

## 三面

```
技术深度，问得特别特别细
看过源码对吧，hadoop的HDFS，Yarn，MapReduce源码讲讲，精确到方法和类。
客户端和NN的通信过程，和DN的通信过程。
HA中的选举过程，如何防止脑裂，为什么需要fencing，SSH连不上怎么办。
checkpoint的过程
yarn的状态机和epoll
yarn的公平调度器和容量调度器和FIFO
yarn抢占
MapReduce全过程，分片怎么读的，为什么用快排，换别的行不行，多路归并怎么实现的，环形缓冲区怎么实现的。
场景，很多牛逼的电脑，大CPU大内存，网卡超烂，如何优化？ 强制本地化，选高压缩的序列化格式，核心就是尽量减少网络IO。
场景，一个加载在内存里的HashMap，Key和Value全是int，从硬盘读进来只做查询不做修改，不考虑查询效率，尽可能提高空间效率，稍微考虑一下时间效率。从JVM调优到数据结构到GC到。。。。
手撕，实现一个线程池。
在spark和hadoop中如何处理数据倾斜。
spark的调度过程，DAGScheduler如何划分stage，TaskScheduler如何调度任务的。Spark的shuffle是什么样的，怎么优化的。
评价一下Hadoop和Spark的代码风格，我曹，我随便说了说，怕送命题。幸好我说完了他就自己说了，疯狂点头。
Java的多线程，讲讲锁。synchronized和reentrantlock的区别。讲讲阻塞队列，volatile
synchronized为啥是非公平的，这个我真不知道，回来一顿狠查，赶紧记住了。
Java的Obejct谈谈
Java的容器谈谈
Java的NIO用得咋样，不咋样。。。那就不聊了。
MPI聊聊
你Scala用得怎么样呀，Python怎么样啊
设计，设计一个RPC框架，我看你简历上做过，谈谈思路，观察者模式讲一下，并发容器。
设计，现在有一个RPC框架，需要使用线程池，多次复用socket，TCP，怎么传递命令。变相考hadoop，使用操作码。
结束。这一面的面试官超强，被压着打。

```

## 四面

```
Um.....和我这个岗没有一毛钱关系。
手写一个leetcode hard，就是两个数组，找全局中间数那个。
问问搜索。
如何构建倒排索引
BM25
如何加速查询，WAND算法细节。
倒排索引的log时间合并
如何评价一次query，设计一个机器学习模型，如何设计这样的模型，如何提取feature，用什么模型
如何做查询补全 Trie
设计一个查询缓存
如何对倒排索引进行空间压缩，我先说了OptPForDelta
如何选择最优分裂点，抽样，然后算。
手撕个代码实现一下
如果不抽样，你这样是全局最优吗？我说是啊，他说不是。。。。对每一个算一个。好吧，他说得对。
还有别的优化方式吗 Variable Byte
聊家常。。。。出了门感觉人生都灰暗了，跪

```

# 美团
作者：Commando20180403011197
链接：https://www.nowcoder.com/discuss/142963?type=2&order=0&pos=5&page=1
来源：牛客网

*数据开发，就是数据仓库开发，好多重复的我不写了。已经拒了
## 一面

```
基础：
讲讲项目
java基础
HTTP和TCP
写俩算法，可以用IDE。都是超简单的，有一个台阶，还有一个m的n次幂，自己写个测试试一下
写个sql
mysql的索引，如何实现的，何时失效，聚簇索引和非聚簇索引，B+树
写个单例，懒汉饿汉。
处理数据倾斜
Spring系列会吗？不会
```

## 二面

```
hadoop和spark简单讲讲，只说结构原理不说源码
spark的调优
JMM，全程不打断，我一口气说到了底。。。
数据仓库的星型模型和雪花模型，上卷下钻
Hive
还有啥忘了
spark sql，窝不会
```

## 三面

```
讲项目
HTTP，TCP
操作系统的进程与线程
Java多线程，线程池
爬虫讲讲原理（我项目里用了）
场景：实现一下chrome里那个历史记录这个功能，用啥数据结构怎么实现
python，MPI，数据仓库，你都会点什么都说说
问了项目里的数据清洗，补全和NLP，然而我的NLP一点深度学习没用，感觉对面可能有点失望。
问了推荐系统
问了flask，Restful API
tensorflow怎么实现的知道么，说了个大概，caffe会用么。不会
```

# 阿里
* 阿里在我投简历两个月之后捞我，而且面试简单得出奇，只考广度不考深度。。。。我还是犯了好多错误，提醒大家，如果面试这种面试官不深问的，一定要自己使劲说，不要说两句就停了，他觉得你只会这么多。

## 一面

```
这个面试官是个做算法的
说个项目
Java基础，说说容器
多线程，线程池
java的序列化方式，hadoop的序列化方式，Avro，parquet，transient关键字
spark的原理，我就说了个架子等他问，他不问了！
设计模式，观察者，hadoop用了啥，我说用了组合，等他问我第一第二关系的事，他不问了！
业务题，拿spark实现个业务逻辑，拿嘴说就行。map然后reducebykey然后。。。
一个白板代码，是他工作业务逻辑的抽象。
还问啥我给忘了
```

## 二面三面开始集中面试

```
连着面了我2小时，我一起写了。
说个项目，问我项目实现细节。工程细节，你这怎么加速，Geohash怎么用的。并对我的辣鸡项目表示了鄙视。
spark streaming和storm，flink的区别
各种分布式计算框架的区别
storm如何处理反压，如何保证流的可靠性的
flink会吗，不会
hbase的row key设计，我现在就要范围查询value怎么设计。
yarn里面机器崩了，怎么让任务接着算。。。我说了重启nodemanager重算这个分片，如果这个分片也不想重算呢，接着算，没这功能没说出来。回来想想其实还是应该能说个思路的。
说了AppMaster的故障怎么实现的HA
zookeeper是如何选举新的Active Namenode，没仔细说，说了个原理他又不继续问了，后悔。
大数算法：10亿个int查重，讲思路，布隆过滤，mapreduce，bitmap，算法的复杂度说说
机器学习竞赛跟我说说，讲了特征工程，提了GBDT，然而我觉得他并不知道GBDT是啥，讲完和我说你这就是java的hello world啊。主要也是自己讲的不好，我也很是哽咽

```

## HR面

```
阿里这个HR面是真的犀利，我也没经验，第一次面这种话里带枪的HR面。
中间委婉谈及了我的学历，我的水课，我的没实习没经历。。。被贬得一无是处，我也不敢顶她，只能避重就轻解释一下。现在回想可能表现得太没自信了更减分。
拿到了意向书。。。。没有谈薪资，没有加面，感觉要拿劝退价了。
```

---

# 2018大数据面试题总结

[2018大数据面试题总结](https://zhuanlan.zhihu.com/p/48624942)

* 1、RDD中reduceBykey与groupByKey哪个性能好，为什么

reduceByKey：

>reduceByKey会在结果发送至reducer之前会对每个mapper在本地进行merge，有点类似于在MapReduce中的combiner。这样做的好处在于，在map端进行一次reduce之后，数据量会大幅度减小，从而减小传输，保证reduce端能够更快的进行结果计算。

groupByKey：

>groupByKey会对每一个RDD中的value值进行聚合形成一个序列(Iterator)，此操作发生在reduce端，所以势必会将所有的数据通过网络进行传输，造成不必要的浪费。同时如果数据量十分大，可能还会造成OutOfMemoryError。

通过以上对比可以发现在进行大量数据的reduce操作时候建议使用reduceByKey。不仅可以提高速度，还是可以防止使用groupByKey造成的内存溢出问题。

* 2、讲述一下hdfs上传文件的流程。

答：这里描述的 是一个256M的文件上传过程

```
① 由客户端 向 NameNode节点节点 发出请求；

②NameNode 向Client返回可以可以存数据的 DataNode 这里遵循机架感应原则；

③客户端 首先 根据返回的信息 先将 文件分块（Hadoop2.X版本 每一个block为 128M 而之前的版本为 64M；

④然后通过NameNode返回的DataNode信息 直接发送给DataNode 并且是 流式写入同时会复制到其他两台机器；

⑤DataNode 向 Client通信 表示已经传完 数据块 同时向NameNode报告

⑥依照上面（④到⑤）的原理将 所有的数据块都上传结束 向 NameNode 报告 表明 已经传完所有的数据块 。
```

* 3、了解zookeeper吗？介绍一下它，它的选举机制和集群的搭建。

答：那当然是熟悉啦，ZooKeeper 是一个开源的分布式协调服务，是 Google Chubby 的开源实现。

> 分布式应用程序可以基于 ZooKeeper 实现诸如数据发布/订阅、负载均衡、命名服务、分布式协调/通知、集群管理、Master 选举、分布式锁和分布式队列等功能。

我们公司使用的flume集群，Kafka集群等等，都离不开ZooKeeper呀。每个节点上我们都要搭建ZooKeeper服务。

>首先我们要在每台pc上配置zookeeper环境变量，在cd到zookeeper下的conf文件夹下在zoo_simjle.cfg文件中添加datadir路径，再到zookeeper下新建data文件夹，创建myid，在文件里添加上server的ip地址。在启动zkserver.sh start便ok了。

* 4、spark streming在实时处理时会发生什么故障，如何停止，解决

答：和Kafka整合时消息无序：

修改Kafka的ack参数，当ack=1时，master确认收到消息就算投递成功。ack=0时，不需要收到消息便算成功，高效不准确。sck=all，master和server都要受到消息才算成功，准确不高效。

StreamingContext.stop会把关联的SparkContext对象也停止，如果不想把SparkContext对象也停止的话可以把StremingContext.stop的可选参数stopSparkContext设为flase。一个SparkContext对象可以和多个streamingcontext对象关联。只要对前一个stremingcontext.stop(stopsparkcontext=false),然后再创建新的stremingcontext对象就可以了。

* 5、说一下你对yarn的理解：

答：YARN是Hadoop2.0版本引进的资源管理系统，直接从MR1演化而来。

核心思想：

>将MR1中的JobTracker的资源管理和作业调度两个功能分开，分别由ResourceManager和ApplicationMaster进程实现。

ResourceManager：

>负责整个集群的资源管理和调度 ；	
>ApplicationMaster：负责应用程序相关事务，比如任务调度、任务监控和容错等。

YARN的出现，使得多个计算框架可以运行在同一个集群之中。 

```
1. 每一个应用程序对应一个ApplicationMaster。 
2. 目前可以支持多种计算框架运行在YARN上面，比如MapReduce、storm、Spark、Flink。
```

* 6、有一个1G大小的一个文件，里面每一行是一个词，词的大小不超过16字节，内存限制大小是1M，要求返回频数最高的100个词。

	：	
>Step1：顺序读文件中，对于每个词x，取hash(x)%5000，然后按照该值存到5000个小文件（记为f0 ,f1 ,... ,f4999）中，这样每个文件大概是200k左右，如果其中的有的文件超过了1M大小，还可以按照类似的方法继续往下分，直到分解得到的小文件的大小都不超过1M；

>Step2：对每个小文件，统计每个文件中出现的词以及相应的频率（可以采用trie树/hash_map等），并取出出现频率最大的100个词（可以用含100个结点的最小堆），并把100词及相应的频率存入文件，这样又得到了5000个文件；

>Step3：把这5000个文件进行归并（类似与归并排序）；

* 7、如何配置spark master的HA？

答：
>1)配置zookeeper

>2)修改spark_env.sh文件,spark的master参数不在指定，添加如下代码到各个master节点

```
ExportSPARK_DAEMON_JAVA_OPTS=-Dspark.deploy.recoveryMode=ZOOKEEPER-

Dspark.deploy.zookeeper.url=zk01:2181,zk02:2181,zk03:2181-Dspark.deploy.zookeeper.dir=/spark”
```

>3) 将spark_env.sh分发到各个节点

>4)找到一个master节点，执行./start-all.sh，会在这里启动主master,其他的master备节点，启动master命令: ./sbin/start-master.sh

>5)提交程序的时候指定master的时候要指定三台master，例如

```
./spark-shell –master spark://master01:7077,master02:7077,master03:7077
```

* 8、一个datanode 宕机,怎么一个流程恢复


答：Datanode宕机了后，如果是短暂的宕机，可以实现写好脚本监控，将它启动起来。如果是长时间宕机了，那么datanode上的数据应该已经被备份到其他机器了，那这台datanode就是一台新的datanode了，删除他的所有数据文件和状态文件，重新启动。

---

# Spark面试题(一)

## 1 Kafka 分布式的情况下，如何保证消息的顺序?
Kafka 分布式的单位是 Partition。如何保证消息有序，需要分几个情况讨论。

* 同一个 Partition 用一个 write ahead log 组织，所以可以保证 FIFO 的顺序。

* 不同 Partition 之间不能保证顺序。但是绝大多数用户都可以通过 message key 来定义，因为同一个 key 的 message 可以保证只发送到同一个 Partition。比如说 key 是 user id，table row id 等等，所以同一个 user 或者同一个 record 的消息永远只会发送到同一个 Partition上，保证了同一个 user 或 record 的顺序。

* 当然，如果你有 key skewness 就有些麻烦，需要特殊处理。

实际情况中: 

```
（1）不关注顺序的业务大量存在；
（2）队列无序不代表消息无序。
```

第（2）条的意思是说: 我们不保证队列的全局有序，但可以保证消息的局部有序。

举个例子: 保证来自同1个 order id 的消息，是有序的！

Kafka 中发送1条消息的时候，可以指定(topic, partition, key) 3个参数。partiton 和 key 是可选的。如果你指定了 partition，那就是所有消息发往同1个 partition，就是有序的。并且在消费端，Kafka 保证，1个 partition 只能被1个 consumer 消费。或者你指定 key（比如 order id），具有同1个 key 的所有消息，会发往同1个 partition。也是有序的。

## 2 对于 Spark 中的数据倾斜问题你有什么好的方案？

简单一句: 

>Spark 数据倾斜的几种场景以及对应的解决方案，包括避免数据源倾斜，调整并行度，使用自定义 Partitioner，使用 Map 侧 Join 代替 Reduce 侧 Join（内存表合并），给倾斜 Key 加上随机前缀等。

* 什么是数据倾斜 	

对 Spark/Hadoop 这样的大数据系统来讲，数据量大并不可怕，可怕的是数据倾斜。数据倾斜指的是，并行处理的数据集中，某一部分（如 Spark 或 Kafka 的一个 Partition）的数据显著多于其它部分，从而使得该部分的处理速度成为整个数据集处理的瓶颈（木桶效应）。

* 数据倾斜是如何造成的 

>在 Spark 中，同一个 Stage 的不同 Partition 可以并行处理，而具有依赖关系的不同 Stage 之间是串行处理的。假设某个 Spark Job 分为 Stage 0和 Stage 1两个 Stage，且 Stage 1依赖于 Stage 0，那 Stage 0完全处理结束之前不会处理Stage 1。而 Stage 0可能包含 N 个 Task，这 N 个 Task 可以并行进行。如果其中 N-1个 Task 都在10秒内完成，而另外一个 Task 却耗时1分钟，那该 Stage 的总时间至少为1分钟。换句话说，一个 Stage 所耗费的时间，主要由最慢的那个 Task 决定。由于同一个 Stage 内的所有 Task 执行相同的计算，在排除不同计算节点计算能力差异的前提下，不同 Task 之间耗时的差异主要由该 Task 所处理的数据量决定。

具体解决方案 

* 1. 调整并行度分散同一个 Task 的不同 Key: Spark 在做 Shuffle 时，默认使用 HashPartitioner（非 Hash Shuffle ???）对数据进行分区。如果并行度设置的不合适，可能造成大量不相同的 Key 对应的数据被分配到了同一个 Task 上，造成该 Task 所处理的数据远大于其它 Task，从而造成数据倾斜。如果调整 Shuffle 时的并行度，使得原本被分配到同一 Task 的不同 Key 发配到不同 Task 上处理，则可降低原 Task 所需处理的数据量，从而缓解数据倾斜问题造成的短板效应。图中左边绿色框表示 kv 样式的数据，key 可以理解成 name。可以看到 Task0 分配了许多的 key，调整并行度，多了几个 Task，那么每个 Task 处理的数据量就分散了。

![](https://pic2.zhimg.com/80/v2-c835c4e277645c663e04b355b124ecb5_hd.jpg)

* 2. 自定义Partitioner: 使用自定义的 Partitioner（默认为 HashPartitioner），将原本被分配到同一个 Task 的不同 Key 分配到不同 Task，可以拿上图继续想象一下，通过自定义 Partitioner 可以把原本分到 Task0 的 Key 分到 Task1，那么 Task0 的要处理的数据量就少了。

 * 3. 将 Reduce side（侧） Join 转变为 Map side（侧） Join: 通过 Spark 的 Broadcast 机制，将 Reduce 侧 Join 转化为 Map 侧 Join，避免 Shuffle 从而完全消除 Shuffle 带来的数据倾斜。可以看到 RDD2 被加载到内存中了。

![](https://pic2.zhimg.com/80/v2-673a656c08704b373fe44d247a22a5b5_hd.jpg)

* 4. 为 skew 的 key 增加随机前/后缀: 为数据量特别大的 Key 增加随机前/后缀，使得原来 Key 相同的数据变为 Key 不相同的数据，从而使倾斜的数据集分散到不同的 Task 中，彻底解决数据倾斜问题。Join 另一则的数据中，与倾斜 Key 对应的部分数据，与随机前缀集作笛卡尔乘积，从而保证无论数据倾斜侧倾斜 Key 如何加前缀，都能与之正常 Join。

* 5. 大表随机添加 N 种随机前缀，小表扩大 N 倍: 如果出现数据倾斜的 Key 比较多，上一种方法将这些大量的倾斜 Key 分拆出来，意义不大（很难一个 Key 一个 Key 都加上后缀）。此时更适合直接对存在数据倾斜的数据集全部加上随机前缀，然后对另外一个不存在严重数据倾斜的数据集整体与随机前缀集作笛卡尔乘积（即将数据量扩大 N 倍），可以看到 RDD2 扩大了 N 倍了，再和加完前缀的大数据做笛卡尔积。

![](https://pic3.zhimg.com/80/v2-95bd9d233cb7431864f47ce6a8f3149e_hd.jpg)

## 3 你所理解的 Spark 的 shuffle 过程？
Spark shuffle 处于一个宽依赖，可以实现类似混洗的功能，将相同的 Key 分发至同一个 Reducer上进行处理。

## 4 Spark有哪些聚合类的算子,我们应该尽量避免什么类型的算子？
在我们的开发过程中，能避免则尽可能避免使用 reduceByKey、join、distinct、repartition 等会进行 shuffle 的算子，尽量使用 map 类的非 shuffle 算子。这样的话，没有 shuffle 操作或者仅有较少 shuffle 操作的 Spark 作业，可以大大减少性能开销。

## 5 spark on yarn 作业执行流程，yarn-client 和 yarn cluster 有什么区别

Spark On Yarn 的优势 
>1. Spark 支持资源动态共享，运行于 Yarn 的框架都共享一个集中配置好的资源池 
2. 可以很方便的利用 Yarn 的资源调度特性来做分类·，隔离以及优先级控制负载，拥有更灵活的调度策略 
3. Yarn 可以自由地选择 executor 数量
4. Yarn 是唯一支持 Spark 安全的集群管理器（Mesos???），使用 Yarn，Spark 可以运行于 Kerberized Hadoop 之上，在它们进程之间进行安全认证

yarn-client 和 yarn cluster 的异同 

>1. 从广义上讲，yarn-cluster 适用于生产环境。而 yarn-client 适用于交互和调试，也就是希望快速地看到 application 的输出。 
2. 从深层次的含义讲，yarn-cluster 和 yarn-client 模式的区别其实就是 Application Master 进程的区别，yarn-cluster 模式下，driver 运行在 AM(Application Master)中，它负责向 YARN 申请资源，并监督作业的运行状况。当用户提交了作业之后，就可以关掉 Client，作业会继续在 YARN 上运行。然而 yarn-cluster 模式不适合运行交互类型的作业。而 yarn-client 模式下，Application Master 仅仅向 YARN 请求 executor，Client 会和请求的 container 通信来调度他们工作，也就是说 Client 不能离开。

![](https://pic1.zhimg.com/80/v2-a1530dc9ac0066a2926b99e4b2d7ae28_hd.jpg)

## 6 Spark为什么快，Spark SQL 一定比 Hive 快吗
Spark SQL 比 Hadoop Hive 快，是有一定条件的，而且不是 Spark SQL 的引擎比 Hive 的引擎快，相反，Hive 的 HQL 引擎还比 Spark SQL 的引擎更快。其实，关键还是在于 Spark 本身快。

* 消除了冗余的 HDFS 读写: Hadoop 每次 shuffle 操作后，必须写到磁盘，而 Spark 在 shuffle 后不一定落盘，可以 cache 到内存中，以便迭代时使用。如果操作复杂，很多的 shufle 操作，那么 Hadoop 的读写 IO 时间会大大增加，也是 Hive 更慢的主要原因了。
* 消除了冗余的 MapReduce 阶段: Hadoop 的 shuffle 操作一定连着完整的 MapReduce 操作，冗余繁琐。而 Spark 基于 RDD 提供了丰富的算子操作，且 reduce 操作产生 shuffle 数据，可以缓存在内存中。
* JVM 的优化: Hadoop 每次 MapReduce 操作，启动一个 Task 便会启动一次 JVM，基于进程的操作。而 Spark 每次 MapReduce 操作是基于线程的，只在启动 Executor 是启动一次 JVM，内存的 Task 操作是在线程复用的。每次启动 JVM 的时间可能就需要几秒甚至十几秒，那么当 Task 多了，这个时间 Hadoop 不知道比 Spark 慢了多少。

记住一种反例 考虑一种极端查询:

>Select month_id, sum(sales) from T group by month_id;

这个查询只有一次 shuffle 操作，此时，也许 Hive HQL 的运行时间也许比 Spark 还快，反正 shuffle 完了都会落一次盘，或者都不落盘。

结论 Spark 快不是绝对的，但是绝大多数，Spark 都比 Hadoop 计算要快。这主要得益于其对 mapreduce 操作的优化以及对 JVM 使用的优化。

## 7 容错方法
## 8 RDD, DAG, Stage怎么理解？

DAG 

>Spark 中使用 DAG 对 RDD 的关系进行建模，描述了 RDD 的依赖关系，这种关系也被称之为 lineage（血缘），RDD 的依赖关系使用 Dependency 维护。DAG 在 Spark 中的对应的实现为 DAGScheduler。

RDD 

>RDD 是 Spark 的灵魂，也称为弹性分布式数据集。一个 RDD 代表一个可以被分区的只读数据集。RDD 内部可以有许多分区(partitions)，每个分区又拥有大量的记录(records)。

Rdd的五个特征： 

```
1. dependencies: 建立 RDD 的依赖关系，主要 RDD 之间是宽窄依赖的关系，具有窄依赖关系的 RDD 可以在同一个 stage 中进行计算。 
2. partition: 一个 RDD 会有若干个分区，分区的大小决定了对这个 RDD 计算的粒度，每个 RDD 的分区的计算都在一个单独的任务中进行。 
3. preferedlocations: 按照“移动数据不如移动计算”原则，在 Spark 进行任务调度的时候，优先将任务分配到数据块存储的位置。 
4. compute: Spark 中的计算都是以分区为基本单位的，compute 函数只是对迭代器进行复合，并不保存单次计算的结果。 
5. partitioner: 只存在于（K,V）类型的 RDD 中，非（K,V）类型的 partitioner 的值就是 None。
```

>RDD 的算子主要分成2类，action 和 transformation。这里的算子概念，可以理解成就是对数据集的变换。action 会触发真正的作业提交，而 transformation 算子是不会立即触发作业提交的。每一个 transformation 方法返回一个新的 RDD。只是某些 transformation 比较复杂，会包含多个子 transformation，因而会生成多个 RDD。这就是实际 RDD 个数比我们想象的多一些 的原因。通常是，当遇到 action 算子时会触发一个job的提交，然后反推回去看前面的 transformation 算子，进而形成一张有向无环图。

Stage 在 DAG 中又进行 stage 的划分，划分的依据是依赖是否是 shuffle 的，每个 stage 又可以划分成若干 task。接下来的事情就是 driver 发送 task 到 executor，executor 自己的线程池去执行这些 task，完成之后将结果返回给 driver。action 算子是划分不同 job 的依据。


## 9 RDD 如何通过记录更新的方式容错
RDD 的容错机制实现分布式数据集容错方法有两种: 

* 1. 数据检查点 
* 2. 记录更新。

RDD 采用记录更新的方式：记录所有更新点的成本很高。所以，RDD只支持粗颗粒变换，即只记录单个块（分区）上执行的单个操作，然后创建某个 RDD 的变换序列（血统 lineage）存储下来；变换序列指，每个 RDD 都包含了它是如何由其他 RDD 变换过来的以及如何重建某一块数据的信息。因此 RDD 的容错机制又称“血统”容错。

## 10 宽依赖、窄依赖怎么理解？
>窄依赖指的是每一个 parent RDD 的 partition 最多被子 RDD 的一个 partition 使用（一子一亲）	

>宽依赖指的是多个子 RDD 的 partition 会依赖同一个 parent RDD的 partition（多子一亲）

RDD 作为数据结构，本质上是一个只读的分区记录集合。一个 RDD 可以包含多个分区，每个分区就是一个 dataset 片段。RDD 可以相互依赖。

首先，窄依赖可以支持在同一个 cluster node上，以 pipeline 形式执行多条命令（也叫同一个 stage 的操作），例如在执行了 map 后，紧接着执行 filter。相反，宽依赖需要所有的父分区都是可用的，可能还需要调用类似 MapReduce 之类的操作进行跨节点传递。

其次，则是从失败恢复的角度考虑。窄依赖的失败恢复更有效，因为它只需要重新计算丢失的 parent partition 即可，而且可以并行地在不同节点进行重计算（一台机器太慢就会分配到多个节点进行），相反，宽依赖牵涉 RDD 各级的多个 parent partition。

## 11 Job 和 Task 怎么理解
Job 

>Spark 的 Job 来源于用户执行 action 操作（这是 Spark 中实际意义的 Job），就是从 RDD 中获取结果的操作，而不是将一个 RDD 转换成另一个 RDD 的 transformation 操作。

Task 一个 Stage 内，最终的 RDD 有多少个 partition，就会产生多少个 task。看一看图就明白了，可以数一数每个 Stage 有多少个 Task。

![](https://pic3.zhimg.com/80/v2-5a7c49140489179d9c78c2d45c27fa22_hd.jpg)


## 12 Spark 血统的概念
RDD 的 lineage 记录的是粗颗粒度的特定数据转换（transformation）操作（filter, map, join etc.)行为。当这个 RDD 的部分分区数据丢失时，它可以通过 lineage 获取足够的信息来重新运算和恢复丢失的数据分区。这种粗颗粒的数据模型，限制了 Spark 的运用场合，但同时相比细颗粒度的数据模型，也带来了性能的提升。

## 13 任务的概念
包含很多 task 的并行计算，可以认为是 Spark RDD 里面的 action，每个 action 的计算会生成一个 job。用户提交的 job 会提交给 DAGScheduler，job 会被分解成 Stage 和 Task。

## 14 容错方法
Spark 选择记录更新的方式。	
但是，如果更新粒度太细太多，那么记录更新成本也不低。因此，RDD只支持粗粒度转换，即只记录单个块上执行的单个操作，然后将创建 RDD 的一系列变换序列（每个 RDD 都包含了他是如何由其他 RDD 变换过来的以及如何重建某一块数据的信息。因此 RDD 的容错机制又称血统容错）记录下来，以便恢复丢失的分区。lineage本质上很类似于数据库中的重做日志（Redo Log），只不过这个重做日志粒度很大，是对全局数据做同样的重做进而恢复数据。

相比其他系统的细颗粒度的内存数据更新级别的备份或者 LOG 机制，RDD 的 lineage 记录的是粗颗粒度的特定数据 transformation 操作行为。当这个 RDD 的部分分区数据丢失时，它可以通过 lineage 获取足够的信息来重新运算和恢复丢失的数据分区。

## 15 Spark 粗粒度和细粒度

如果问的是操作的粗细粒度，应该是，Spark 在错误恢复的时候，只需要粗粒度的记住 lineage，就可实现容错。

关于 Mesos

1. 粗粒度模式（Coarse-grained Mode）: 

>每个应用程序的运行环境由一个 dirver 和若干个 executor 组成，其中，每个 executor 占用若干资源，内部可运行多个 task（对应多少个 slot）。应用程序的各个任务正式运行之前，需要将运行环境中的资源全部申请好，且运行过程中要一直占用这些资源，即使不用，最后程序运行结束后，回收这些资源。

>举个例子，比如你提交应用程序时，指定使用5个 executor 运行你的应用程序，每个 executor 占用5GB内存和5个 CPU，每个 executor 内部设置了5个 slot，则 Mesos 需要先为 executor 分配资源并启动它们，之后开始调度任务。另外，在程序运行过程中，Mesos 的 master 和 slave 并不知道 executor 内部各个 task 的运行情况，executor 直接将任务状态通过内部的通信机制汇报给 driver，从一定程度上可以认为，每个应用程序利用 Mesos 搭建了一个虚拟集群自己使用。 

* 2. 细粒度模式（Fine-grained Mode）:

>鉴于粗粒度模式会造成大量资源浪费，Spark On Mesos 还提供了另外一种调度模式：细粒度模式，这种模式类似于现在的云计算，思想是按需分配。与粗粒度模式一样，应用程序启动时，先会启动 executor，但每个 executor 占用资源仅仅是自己运行所需的资源，不需要考虑将来要运行的任务，之后，Mesos 会为每个 executor 动态分配资源，每分配一些，便可以运行一个新任务，单个 Task 运行完之后可以马上释放对应的资源。每个 Task 会汇报状态给 Mesos slave 和 Mesos Master，便于更加细粒度管理和容错，这种调度模式类似于 MapReduce 调度模式，每个 task 完全独立，优点是便于资源控制和隔离，但缺点也很明显，短作业运行延迟大。

Spark中，每个 application 对应一个 SparkContext。

对于 SparkContext 之间的调度关系，取决于 Spark 的运行模式。	
对 Standalone 模式而言，Spark Master 节点先计算集群内的计算资源能否满足等待队列中的应用对内存和 CPU 资源的需求，如果可以，则 Master 创建 Spark Driver，启动应用的执行。宏观上来讲，这种对应用的调度类似于 FIFO 策略。在 Mesos 和 Yarn 模式下，底层的资源调度系统的调度策略都是由 Mesos 和 Yarn 决定的。具体分类描述如下：

* Standalone 模式: 

> 默认以用户提交 Applicaiton 的顺序来调度，即 FIFO 策略。每个应用执行时独占所有资源。如果有多个用户要共享集群资源，则可以使用参数 spark.cores.max 来配置应用在集群中可以使用的最大 CPU 核的数量。如果不配置，则采用默认参数 spark.deploy.defaultCore 的值来确定。

* Mesos 模式: 

```
如果在 Mesos 上运行 Spark，用户想要静态配置资源的话，可以设置 spark.mesos.coarse 为 true，
这样 Mesos 变为粗粒度调度模式。
然后可以设置 spark.cores.max 指定集群中可以使用的最大核数，与上面 Standalone 模式类似。
同时，在 Mesos 模式下，用户还可以设置参数 spark.executor.memory 来配置每个 executor 的内存使用量。
如果想使 Mesos 在细粒度模式下运行，可以通过 mesos://<url-info> 设置动态共享 CPU core 的执行模式。
在这种模式下，应用不执行时的空闲 CPU 资源得以被其他用户使用，提升了 CPU 使用率。
```

## 16 Spark优越性
一、Spark 的5大优势： 

```
1. 更高的性能。因为数据被加载到集群主机的分布式内存中。数据可以被快速的转换迭代，并缓存用以后续的频繁访问需求。在数据全部加载到内存的情况下，Spark可以比Hadoop快100倍，在内存不够存放所有数据的情况下快hadoop10倍。 
 2. 通过建立在Java,Scala,Python,SQL（应对交互式查询）的标准API以方便各行各业使用，同时还含有大量开箱即用的机器学习库。
 3. 与现有Hadoop 1和2.x(YARN)生态兼容，因此机构可以无缝迁移。 
 4. 方便下载和安装。方便的shell（REPL: Read-Eval-Print-Loop）可以对API进行交互式的学习。 
 5. 借助高等级的架构提高生产力，从而可以讲精力放到计算上。
```

二、MapReduce与Spark相比，有哪些异同点： 
1、基本原理上： 

```
（1） MapReduce：基于磁盘的大数据批量处理系统 
（2）Spark：基于RDD(弹性分布式数据集)数据处理，显示将RDD数据存储到磁盘和内存中。 
```

2、模型上： 

```
（1） MapReduce可以处理超大规模的数据，适合日志分析挖掘等较少的迭代的长任务需求，结合了数据的分布式的计算。 
（2） Spark：适合数据的挖掘，机器学习等多轮迭代式计算任务。
```

在Spark中，一个应用程序包含多个job任务，在MapReduce中，一个job任务就是一个应用（？？？）


## 17 Transformation和action是什么？区别？举几个常用方法
RDD 创建后就可以在 RDD 上进行数据处理。
RDD 支持两种操作: 

```
1. 转换（transformation）: 即从现有的数据集创建一个新的数据集 
2. 动作（action）: 即在数据集上进行计算后，返回一个值给 Driver 程序
```

RDD 的转化操作是返回一个新的 RDD 的操作，比如 map() 和 filter() ，而行动操作则是向驱动器程序返回结果或把结果写入外部系统的操作，会触发实际的计算，比如 count() 和 first() 。

Spark 对待转化操作和行动操作的方式很不一样，因此理解你正在进行的操作的类型是很重要的。如果对于一个特定的函数是属于转化操作还是行动操作感到困惑，你可以看看它的返回值类型：转化操作返回的是 RDD，而行动操作返回的是其他的数据类型。

RDD 中所有的 Transformation 都是惰性的，也就是说，它们并不会直接计算结果。相反的它们只是记住了这些应用到基础数据集（例如一个文件）上的转换动作。只有当发生一个要求返回结果给 Driver 的 Action 时，这些 Transformation 才会真正运行。

这个设计让 Spark 更加有效的运行。

## 18 Spark作业提交流程是怎么样的


* spark-submit 提交代码，执行 new SparkContext()，在 SparkContext 里构造 DAGScheduler 和 TaskScheduler。

* TaskScheduler 会通过后台的一个进程，连接 Master，向 Master 注册 Application。

* Master 接收到 Application 请求后，会使用相应的资源调度算法，在 Worker 上为这个 Application 启动多个 Executer。

* Executor 启动后，会自己反向注册到 TaskScheduler 中。 所有 Executor 都注册到 Driver 上之后，SparkContext 结束初始化，接下来往下执行我们自己的代码。

* 每执行到一个 Action，就会创建一个 Job。Job 会提交给 DAGScheduler。

* DAGScheduler 会将 Job划分为多个 stage，然后每个 stage 创建一个 TaskSet。

* TaskScheduler 会把每一个 TaskSet 里的 Task，提交到 Executor 上执行。

* Executor 上有线程池，每接收到一个 Task，就用 TaskRunner 封装，然后从线程池里取出一个线程执行这个 task。(TaskRunner 将我们编写的代码，拷贝，反序列化，执行 Task，每个 Task 执行 RDD 里的一个 partition)

## 19 Spark streamning工作流程是怎么样的，和Storm比有什么区别

Spark Streaming 与 Storm 都可以用于进行实时流计算。但是他们两者的区别是非常大的。

其中区别之一，就是，Spark Streaming 和 Storm 的计算模型完全不一样，Spark Streaming 是基于 RDD 的，因此需要将一小段时间内的，比如1秒内的数据，收集起来，作为一个 RDD，然后再针对这个 batch 的数据进行处理。

而 Storm 却可以做到每来一条数据，都可以立即进行处理和计算。

因此，Spark Streaming 实际上严格意义上来说，只能称作准实时的流计算框架；而 Storm 是真正意义上的实时计算框架。

此外，Storm 支持的一项高级特性，是 Spark Streaming 暂时不具备的，即 Storm 支持在分布式流式计算程序（Topology）在运行过程中，可以动态地调整并行度，从而动态提高并发处理能力。

而 Spark Streaming 是无法动态调整并行度的。 但是 Spark Streaming 也有其优点，首先 Spark Streaming 由于是基于 batch 进行处理的，因此相较于 Storm 基于单条数据进行处理，具有数倍甚至数十倍的吞吐量。 

此外，Spark Streaming 由于也身处于 Spark 生态圈内，因此Spark Streaming可以与Spark Core、Spark SQL，甚至是Spark MLlib、Spark GraphX进行无缝整合。

流式处理完的数据，可以立即进行各种map、reduce转换操作，可以立即使用sql进行查询，甚至可以立即使用machine learning或者图计算算法进行处理。这种一站式的大数据处理功能和优势，是Storm无法匹敌的。 

因此，综合上述来看，通常在对实时性要求特别高，而且实时数据量不稳定，比如在白天有高峰期的情况下，可以选择使用Storm。但是如果是对实时性要求一般，允许1秒的准实时处理，而且不要求动态调整并行度的话，选择Spark Streaming是更好的选择。

![](https://pic1.zhimg.com/80/v2-870c1d28b0d1f89263af0645ca1a70f0_hd.jpg)

## 20 Spark sql你使用过没有，在哪个项目里面使用的
离线 ETL 之类的，结合机器学习等

## 21 Spark 机器学习和 Spark 图计算接触过没有，能举例说明你用它做过什么吗？
Spark 提供了很多机器学习库，我们只需要填入数据，设置参数就可以用了。使用起来非常方便。 
另外一方面，由于它把所有的东西都写到了内部，我们无法修改其实现过程。要想修改里面的某个环节，还的修改源码，重新编译。 
比如 kmeans 算法，如果没有特殊需求，很方便。但是spark内部使用的两个向量间的距离是欧式距离。如果你想改为余弦或者马氏距离，就的重新编译源码了。
Spark 里面的机器学习库都是一些经典的算法，这些代码网上也好找。这些代码使用起来叫麻烦，但是很灵活。 Spark 有一个很大的优势，那就是 RDD。

模型的训练完全是并行的。

Spark 的 ML 和 MLLib 两个包区别和联系

* 技术角度上，面向的数据集类型不一样: ML 的 API 是面向 Dataset 的（Dataframe 是 Dataset 的子集，也就是 Dataset[Row]）， mllib 是面对 RDD 的。
* Dataset 和 RDD 有啥不一样呢？Dataset 的底端是 RDD。Dataset 对 RDD 进行了更深一层的优化，比如说有 sql 语言类似的黑魔法，Dataset 支持静态类型分析所以在 compile time 就能报错，各种 combinators（map，foreach 等）性能会更好，等等。
* 编程过程上，构建机器学习算法的过程不一样: ML 提倡使用 pipelines，把数据想成水，水从管道的一段流入，从另一端流出。ML 是1.4比 Mllib 更高抽象的库，它解决如果简洁的设计一个机器学习工作流的问题，而不是具体的某种机器学习算法。未来这两个库会并行发展。

## 22 Spark RDD是怎么容错的，基本原理是什么？

[RDD之七：Spark容错机制](https://www.cnblogs.com/duanxz/p/6329675.html)

## 23 为什么要用Yarn来部署Spark?

因为 Yarn 支持动态资源配置。
Standalone 模式只支持简单的固定资源分配策略，每个任务固定数量的 core，各 Job 按顺序依次分配在资源，资源不够的时候就排队。
这种模式比较适合单用户的情况，多用户的情境下，会有可能有些用户的任务得不到资源。

Yarn 作为通用的种子资源调度平台，除了 Spark 提供调度服务之外，还可以为其他系统提供调度，如 Hadoop MapReduce, Hive 等。

## 24 说说yarn-cluster和yarn-client的异同点。

* cluster 模式会在集群的某个节点上为 Spark 程序启动一个称为 Master 的进程，然后 Driver 程序会运行在这个 Master 进程内部，由这种进程来启动 Driver 程序，客户端完成提交的步骤后就可以退出，不需要等待 Spark 程序运行结束，这是适合生产环境的运行方式
* client 模式也有一个 Master 进程，但是 Driver 程序不会运行在这个 Master 进程内部，而是运行在本地，只是通过 Master 来申请资源，直到运行结束，这种模式非常适合需要交互的计算。显然 Driver 在 client 模式下会对本地资源造成一定的压力。

## 25 解释一下 groupByKey, reduceByKey 还有 reduceByKeyLocally
From: http://lxw1234.com/archives/2015/07/360.htm

## 26 说说 persist() 和 cache() 的异同
可以从源码上解释

## 27 可以解释一下这两段程序的异同吗

```
val counter = 0
val data = Seq(1, 2, 3)
data.foreach(x => counter += x)
println("Counter value: " + counter)


val counter = 0
val data = Seq(1, 2, 3)
var rdd = sc.parallelizze(data)
rdd.foreach(x => counter += x)
println("Counter value: " + counter)
```

所有在 Driver 程序追踪的代码看上去好像在 Driver 上计算，实际上都不在本地，每个 RDD 操作都被转换成 Job 分发至集群的执行器 Executor 进程中运行，即便是单机本地运行模式，也是在单独的执行器进程上运行，与 Driver 进程属于不用的进程。所以每个 Job 的执行，都会经历序列化、网络传输、反序列化和运行的过程。

再具体一点解释是 foreach 中的匿名函数 x => counter += x 首先会被序列化然后被传入计算节点，反序列化之后再运行，因为 foreach 是 Action 操作，结果会返回到 Driver 进程中。

在序列化的时候，Spark 会将 Job 运行所依赖的变量、方法全部打包在一起序列化，相当于它们的副本，所以 counter 会一起被序列化，然后传输到计算节点，是计算节点上的 counter 会自增，而 Driver 程序追踪的 counter 则不会发生变化。执行完成之后，结果会返回到 Driver 程序中。而 Driver 中的 counter 依然是当初的那个 Driver 的值为0。

因此说，RDD 操作不能嵌套调用，即在 RDD 操作传入的函数参数的函数体中，不可以出现 RDD 调用。

## 28 说说map和mapPartitions的区别

```
map 中的 func 作用的是 RDD 中每一个元素，
而 mapPartitions 中的 func 作用的对象是 RDD 的一整个分区。
所以 func 的类型是 Iterator<T> => Iterator<T>，其中 T 是输入 RDD 的元素类型。
```

## 29 groupByKey和reduceByKey是属于Transformation还是 Action？
前者，因为 Action 输出的不再是 RDD 了，也就意味着输出不是分布式的，而是回送到 Driver 程序。
以上两种操作都是返回 RDD，所以应该属于 Transformation。

## 30 说说Spark支持的3种集群管理器
* Standalone 模式: 资源管理器是 Master 节点，调度策略相对单一，只支持先进先出模式。
* Hadoop Yarn 模式: 资源管理器是 Yarn 集群，主要用来管理资源。Yarn 支持动态资源的管理，还可以调度其他实现了 Yarn 调度接口的集群计算，非常适用于多个集群同时部署的场景，是目前最流行的一种资源管理系统。
* Apache Mesos: Mesos 是专门用于分布式系统资源管理的开源系统，与 Yarn 一样是 C++ 开发，可以对集群中的资源做弹性管理。

## 31 说说Worker和Excutor的异同
Worker 是指每个及节点上启动的一个进程，负责管理本节点，jps 可以看到 Worker 进程在运行。

Excutor 每个Spark 程序在每个节点上启动的一个进程，专属于一个 Spark 程序，与 Spark 程序有相同的生命周期，负责 Spark 在节点上启动的 Task，管理内存和磁盘。

如果一个节点上有多个 Spark 程序，那么相应就会启动多个执行器。

## 32 说说Spark提供的两种共享变量
Spark 程序的大部分操作都是 RDD 操作，通过传入函数给 RDD 操作函数来计算，这些函数在不同的节点上并发执行，内部的变量有不同的作用域，不能相互访问，有些情况下不太方便。

* 广播变量，是一个只读对象，在所有节点上都有一份缓存，创建方法是 SparkContext.broadcast()。创建之后再更新它的值是没有意义的，一般用 val 来修改定义。
* 计数器，只能增加，可以用计数或求和，支持自定义类型。创建方法是 SparkContext.accumulator(V, name)。只有 Driver 程序可以读这个计算器的变量，RDD 操作中读取计数器变量是无意义的。但节点可以对该计算器进行增加（？？？）
以上两种类型都是 Spark 的共享变量。

## 33 说说检查点的意义
分布式编程中经常需要做检查点，即将某个时机的中间数据写到存储中。

## 34 Master容错类型
## 35 解释一下Spark Master的选举过程
## 36 说说Spark如何实现序列化组件的
## 37 说说对Master的理解

Master 是 local-cluster 部署模式和 Standalone 部署模式中，整个 Spark 集群最为重要的组件之一，分担了对整个集群资源的管理和分配的工作。

local-cluser 下，Master 作为 JVM 进程的对象启动，而在 Standalone 模式下，就是单独的进程启动。

## 38 说说什么是窗口间隔和滑动间隔
对于窗口操作，在其窗口内部会有 N 个批处理数据，批处理数据的个数由窗口间隔决定，其为窗口持续的时间，在窗口操作中只有窗口间隔满足了才会触发批数据的处理（指一开始的阶段）。

滑动间隔是指经过多长时间窗口滑动一次形成新的窗口，滑动传功库默认情况下和批次间隔的相同，而窗口间隔一般设置得要比它们都大。

## 39 说说Spark的预写日志功能
也叫 WriteAheadLogs，通常被用于数据库和文件系统中，保证数据操作的持久性。

预写日志通常是先将操作写入到一个持久可靠的日志文件中，然后才对数据施加该操作，当加入施加该操作中出现异常，可以通过读取日志文件并重新施加该操作，从而恢复系统。

当 WAL 开启后，所有收到的数据同时保存到了容错文件系统的日志文件中，当 Spark Streaming 失败，这些接受到的数据也不会丢失。另外，接收数据的正确性只在数据被预写到日志以后接收器才会确认。已经缓存但还没有保存的数据可以在 Driver 重新启动之后由数据源再发送一次（经常问）。

这两个机制保证了数据的零丢失，即所有的数据要么从日志中恢复，要么由数据源重发。

## 40 Spark Streaming小文件问题

使用 Spark Streaming 时，如果实时计算结果要写入到 HDFS，那么不可避免的会遇到一个问题，那就是在默认情况下会产生非常多的小文件，这是由 Spark Streaming 的微批处理模式和 DStream(RDD) 的分布式(partition)特性导致的，Spark Streaming 为每个 Partition 启动一个独立的线程（一个 task/partition 一个线程）来处理数据，一旦文件输出到 HDFS，那么这个文件流就关闭了，再来一个 batch 的 parttition 任务，就再使用一个新的文件流，那么假设，一个 batch 为10s，每个输出的 DStream 有32（最大？？？）个 partition，那么一个小时产生的文件数将会达到(3600/10)*32=11520个之多。众多小文件带来的结果是有大量的文件元信息，比如文件的 location、文件大小、block number 等需要 NameNode 来维护，NameNode 会因此鸭梨山大。不管是什么格式的文件，parquet、text、JSON 或者 Avro，都会遇到这种小文件问题，这里讨论几种处理 Spark Streaming 小文件的典型方法。

* 增加 batch 大小: 这种方法很容易理解，batch 越大，从外部接收的 event 就越多，内存积累的数据也就越多，那么输出的文件数也就会变少，比如上边的时间从10s增加为100s，那么一个小时的文件数量就会减少到1152个。但别高兴太早，实时业务能等那么久吗，本来人家10s看到结果更新一次，现在要等快两分钟，是人都会骂娘。所以这种方法适用的场景是消息实时到达，但不想挤压在一起处理，因为挤压在一起处理的话，批处理任务在干等，这时就可以采用这种方法。
* Coalesce大法好: 文章开头讲了，小文件的基数是 batch_number * partition_number，而第一种方法是减少 batch_number，那么这种方法就是减少 partition_number 了，这个 api 不细说，就是减少初始的分区个数。看过 spark 源码的童鞋都知道，对于窄依赖，一个子 RDD 的 partition 规则继承父 RDD，对于宽依赖(就是那些个叉叉叉ByKey操作)，如果没有特殊指定分区个数，也继承自父 rdd。那么初始的 SourceDstream 是几个 partiion，最终的输出就是几个 partition。所以 Coalesce 大法的好处就是，可以在最终要输出的时候，来减少一把 partition 个数。但是这个方法的缺点也很明显，本来是32个线程在写256M数据，现在可能变成了4个线程在写256M数据，而没有写完成这256M数据，这个 batch 是不算结束的。那么一个 batch 的处理时延必定增长，batch 挤压会逐渐增大。
* Spark Streaming 外部来处理: 我们既然把数据输出到 hdfs，那么说明肯定是要用 Hive 或者 Spark Sql 这样的“sql on hadoop”系统类进一步进行数据分析，而这些表一般都是按照半小时或者一小时、一天，这样来分区的(注意不要和 Spark Streaming 的分区混淆，这里的分区，是用来做分区裁剪优化的)，那么我们可以考虑在 Spark Streaming 外再启动定时的批处理任务来合并 Spark Streaming 产生的小文件。这种方法不是很直接，但是却比较有用，“性价比”较高，唯一要注意的是，批处理的合并任务在时间切割上要把握好，搞不好就可能会去合并一个还在写入的 Spark Streaming 小文件。
* 自己调用 foreach 去 append: Spark Streaming 提供的 foreach 这个 outout 类 api （一种 Action 操作），可以让我们自定义输出计算结果的方法。那么我们其实也可以利用这个特性，那就是每个 batch 在要写文件时，并不是去生成一个新的文件流，而是把之前的文件打开。考虑这种方法的可行性，首先，HDFS 上的文件不支持修改，但是很多都支持追加，那么每个 batch 的每个 partition 就对应一个输出文件，每次都去追加这个 partition 对应的输出文件，这样也可以实现减少文件数量的目的。这种方法要注意的就是不能无限制的追加，当判断一个文件已经达到某一个阈值时，就要产生一个新的文件进行追加了。所以大概就是一直32个文件咯。



---

# spark streaming ,预写日志（write-ahead logging）和checkpoint

[spark streaming ,预写日志（write-ahead logging）和checkpoint](https://blog.csdn.net/j904538808/article/details/80112805)

## （1）什么是Spark-Streaming？
spark steaming 是spark Core API的一种扩展，它可用于大规模、高吞吐量、容错的实时数据流处理。它支持从多种数据源中读取数据，如 kafka,flume,twitter,zeromq,kinesis 或者是tcp socket。并且能够使用类似高阶函数的复杂算法来进行数据处理，如 map(),reduce(),join(),window(）等。处理后的数据可以保存到文件系统、数据库等。

## （2）spark streaming的基本运行原理？
接收实时输入数据流，将数据流按照时间间隔拆分成多个batch,比如每收集5s的数据封装成一个batch,然后将每个batch交给spark的计算引擎进行处理，产生由一个一个的batch组成的结果数据流，输出。

## （3）spark streaming的高级抽象？
spark streaming提供的高级抽象叫做Dstream，即 Discretized Stream(离散流) 
代表了一个持续不断的数据流，Dstream可以利用数据源来创建，如kafka,flume,twitter,zeromq,kinesis，也可以通过其他的Dstream通过高阶函数来创建，如map(),reduce(),join(),window(）等，自我的理解是类似于RDD的创建，可以根据数据本身来创建，也可以根据RDD通过map(),reduce(),join()等来创建，Dstream类似于一个装满RDD元素的RDD列表的感觉。Dstream的内部其实是一系列持续不断产生的RDD，RDD是不可变的分布式的数据集，Dstream 中的每个RDD都包含了一个时间段内的数据。

## （4）spark streaming checkpoint的概述？
每一个spark streaming应用程序，都需要持续不断的进行运转计算，不可停歇，因此就必须有对与应用逻辑无关的失败的容错能力，spark streaming就要将错的信息checkpoint在容错的存储系统上，是应用程序运行时，能够从错败中恢复。

## （5）如何对Dstream checkpoint?
首先，设置还原点目录； 
其次，调用Dstream的checkpoint方法； 
Dstream的checkpoint周期一定要是产生batch的整数倍，官方建议设置至少10s,通常来说设置为窗口滑动间隔的5~10倍比较合适。

## （6）spark streaming过程中，什么数据要被checkpoint？

* （1）元数据信息。 

包括： 

```
（1.1）配置信息—创建Spark-Streaming应用程序的配置信息，比如SparkConf 
（1.2）DStream的操作信息—-定义了Spark-Stream应用程序的计算逻辑的DStream操作信息 
（1.3）未处理的batch信息—-哪些job正在排队，还没处理的batch信息。 
```

* （2）实时计算中产生的RDD数据checkpoint。

对于一些将多个batch的数据进行聚合的，有状态的transformation操作，这是非常有用的， 
在这种tranformation操作中，生成的RDD是依赖与之前的batch的，这会导致随着时间的推移，Rdd的依赖 
链条越来越长，要避免由于依赖链条越来越长，导致一起变得越来越长的失败恢复时间，有状态的transformation 
操作执行过程中间产生的RDD，会定期的被checkpoint到可靠的存储系统上,比如HDFS,从而削减RDD的依赖链条，进而缩短失败恢复时RDD的恢复时间。

## （8）预写日志是什么？其工作机制是什么？如何配置预写日志机制？

预写日志，简称WAL,从spark1.2版本开始，引入基于容错机制的文件系统的WAL机制，若启用该机制，receiver接收到的所有数据都会被写入配置的checkpoint目录中的预写日志这种机制可以让driver在恢复的时候，避免数据丢失，并且可以确保整个实时计算过程中，零数据丢失。 
要配置该机制： 

```
首先，调用StreamingContext的checkpoint()方法设置一个checkpoint目录； 
然后，将spark.streaming.receiver.writeAheadLog.enable参数设置为true。
```

WAL机制的缺点： 

```
会导致Receiver的吞吐量大幅度下降，因为单位时间内有相当一部分时间需要将数据写入预写日志； 
```

解决办法： 

>如果又希望开启预写日志机制，确保数据零损失，又不希望影响系统的吞吐量，那么可以创建多个输入DStream启动多个Reciver，然后将这些receiver接收到的数据使用ssc.union()方法将这些dstream中的数据进行合并 。此外在启用了预写日志机制之后，推荐将复制持久化机制禁用掉，因为所有数据已经保存在容错的文件系统中了，不需要再用复制机制进行持久化，保存一份副本了，只要将输入的DStream的持久化机制设置一下即可

---

---



# 其他

## [Java对象内存占用分析](https://segmentfault.com/a/1190000006933272)

本文深入分析并验证了不同Java对象占用内存空间大小的情况。对于不同的jvm实现，Java对象占用的内存空间大小可能不尽相同，本文主要分析HotSpot jvm中的情况，实验环境为64位window10系统、JDK1.8，使用JProfiler进行结论验证。

### Java对象内存布局

Java对象的内存布局包括：对象头(Header)，实例数据(Instance Data)和补齐填充(Padding)。

### 对象头：

关于对象头的详细介绍可以参看我的博文：[http://blog.csdn.net/codersha...](http://blog.csdn.net/codershamo/article/details/52071996)，这里只关注其内存占用大小。在64位机器上，默认不开启指针压缩（-XX:-UseCompressedOops）的情况下，对象头占用16bytes，开启指针压缩（-XX:+UseCompressedOops）则占用12bytes。

###  实例数据：

原生类型（primitive type）的内存占用如下：

| Primitive Type | Memory Required(bytes) |
| -------------- | ---------------------- |
| byte, boolean  | 1 byte                 |
| short, char    | 2 bytes                |
| int, float     | 4 bytes                |
| long, double   | 8 bytes                |

对象引用（reference）类型在64位机器上，关闭指针压缩时占用8bytes， 开启时占用4bytes。

### 对齐填充：

Java对象占用空间是8字节对齐的，即所有Java对象占用bytes数必须是8的倍数。例如，一个包含两个属性的对象：int和byte，并不是占用17bytes(12+4+1)，而是占用24bytes（对17bytes进行8字节对齐）

### Java对象内存占用

首先根据以上的计算规则，进行一个简单的验证。使用下面的程序进行验证：

```java
public class Test {
    public static void main(String[] args) throws InterruptedException {
        TestObject testObject = new TestObject();

        Thread.sleep(600 * 1000);
        System.out.println(testObject);
    }
}

class TestObject {
    private int i;
    private double d;
    private char[] c;

    public TestObject() {
        this.i = 1;
        this.d = 1.0;
        this.c = new char[]{'a', 'b', 'c'};
    }
}
```

TestObject对象有四个属性，分别为int, double, Byte, char[]类型。在打开指针压缩(-XX:+UseCompressedOops)的情况下，在64位机器上，TestObject占用的内存大小应为：

> 12(Header) + 4(int) + 8(double) + 4(reference) = 28 (bytes)，加上8字节对齐，最终的大小应为32 bytes。

JProfiler中的结果为：

![clipboard.png](https://segmentfault.com/img/bVDf3h?w=746&h=191)

可以看到,TestObject占用的内存空间大小（Shallow Size）为32 bytes。

关于Retained Size和Shallow Size的区别，可以参看：
[http://blog.csdn.net/e5945/ar...](http://blog.csdn.net/e5945/article/details/7708253)

当指针压缩关闭时(-XX:-UseCompressedOops)，在64位机器上，TestObject占用的内存大小应为：

> 16(Header) + 4(int) + 8(double) + 8(reference) = 36 (bytes), 8字节对齐后为 40 bytes。

JProfile的结果为：

![clipboard.png](https://segmentfault.com/img/bVDf5q?w=735&h=183)

### **包装类型**

包装类（Boolean/Byte/Short/Character/Integer/Long/Double/Float）占用内存的大小等于对象头大小加上底层基础数据类型的大小。

包装类型的对象内存占用情况如下：

| Numberic Wrappers | +useCompressedOops | -useCompressedOops |
| ----------------- | ------------------ | ------------------ |
| Byte, Boolean     | 16 bytes           | 24 bytes           |
| Short, Character  | 16 bytes           | 24 bytes           |
| Integer, Float    | 16 bytes           | 24 bytes           |
| Long, Double      | 24 bytes           | 24 bytes           |

### **数组**

64位机器上，数组对象的对象头占用24 bytes，启用压缩后占用16字节。比普通对象占用内存多是因为需要额外的空间存储数组的长度。基础数据类型数组占用的空间包括数组对象头以及基础数据类型数据占用的内存空间。由于对象数组中存放的是对象的引用，所以对象数组本身的大小=数组对象头+length * 引用指针大小，总大小为对象数组本身大小+存放的数据的大小之和。

举两个例子：

- int[10]: 
  开启压缩：16 + 10 * 4 = 56 bytes；

![clipboard.png](https://segmentfault.com/img/bVDgBP?w=717&h=318)

关闭压缩：24 + 10 * 4 = 64bytes。

![clipboard.png](https://segmentfault.com/img/bVDgCA?w=724&h=344)

- new Integer[3]:

```
    关闭压缩：
    Integer数组本身：24(header) + 3 * 8(Integer reference) = 48 bytes;
    总共：48 + 3 * 24(Integer) = 120 bytes。
```

![clipboard.png](https://segmentfault.com/img/bVDgFT?w=729&h=263)

```
    开启压缩：
    Integer数组本身：16(header) + 3 * 4(Integer reference) = 28(padding) -> 32 (bytes)
    总共：32 + 3 * 16(Integer) = 80 (bytes)
```

![clipboard.png](https://segmentfault.com/img/bVDgGC?w=732&h=267)

### **String**

在JDK1.7及以上版本中，String包含2个属性，一个用于存放字符串数据的char[], 一个int类型的hashcode, 部分源代码如下：

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];

    /** Cache the hash code for the string */
    private int hash; // Default to 0
}
```

因此，在关闭指针压缩时，一个String本身需要 16(Header) + 8(char[] reference) + 4(int) = 32 bytes。
除此之外，一个char[]占用24 + length * 2 bytes(8字节对齐), 即一个String占用的内存空间大小为：

> 56 + length * 2 bytes (8字节对齐)。
> 举几个例子。

- 一个空字符串("")的大小应为：56 + 0 * 2 bytes = 56 bytes。
  JProfiler结果：

![clipboard.png](https://segmentfault.com/img/bVDgeh?w=714&h=175)

- 字符串"abc"的大小应为：56 + 3 * 2 = 62(8字节对齐)->64 (bytes)

![clipboard.png](https://segmentfault.com/img/bVDgem?w=724&h=178)

- 字符串"abcde"的大小应为：56 + 5 * 2 = 66->72 (bytes)

![clipboard.png](https://segmentfault.com/img/bVDgeq?w=721&h=172)

- 字符串"abcde"在开启指针压缩时的大小为：

```
   String本身：12(Header) + 4(char[] reference) + 4(int hash) = 20(padding) -> 24 (bytes);   
   存储数据：16(char[] header) + 5*2 = 26(padding) -> 32 (bytes)         
   总共：24 + 32 = 56 (bytes)  
```

![clipboard.png](https://segmentfault.com/img/bVDgff?w=718&h=200)

### 常用数据结构内存占用

根据上面的内存占用计算规则，可以计算出一个对象在内存中的占用空间大小情况，下面举例分析下Java中的Enum, ArrayList及HashMap的内存占用情况，读者可以仿照分析计算过程来计算其他数据结构的内存占用情况。

**注：** 下面的分析计算基于HotSpot Jvm, JDK1.8, 64位机器，开启指针压缩。

####  枚举类

创建enum时，编译器会生成一个相关的类，这个类继承自java.lang.Enum。Enum类拥有两个属性变量，分别为int的ordinal和String的name, 相关源码如下：

```java
public abstract class Enum<E extends Enum<E>>
        implements Comparable<E>, Serializable {
    /**
     * The name of this enum constant, as declared in the enum declaration.
     * Most programmers should use the {@link #toString} method rather than
     * accessing this field.
     */
    private final String name;

    /**
     * The ordinal of this enumeration constant (its position
     * in the enum declaration, where the initial constant is assigned
     * an ordinal of zero).
     *
     * Most programmers will have no use for this field.  It is designed
     * for use by sophisticated enum-based data structures, such as
     * {@link java.util.EnumSet} and {@link java.util.EnumMap}.
     */
    private final int ordinal;
}
```

以下面的TestEnum为例进行枚举类的内存占用分析

```java
public enum TestEnum {
        ONE(1, "one"),
        TWO(2, "two");

        private int code;
        private String desc;

        TestEnum(int code, String desc) {
            this.code = code;
            this.desc = desc;
        }

        public int getCode() {
            return code;
        }

        public String getDesc() {
            return desc;
        }
}
```

这里TestEnum的每个实例除了父类的两个属性外，还拥有一个int的code及String的desc属性，所以一个TestEnum的实例本身所占用的内存大小为：

> 12(header) + 4(ordinal) + 4(name reference) + 4(code) + 4(desc reference) = 28(padding) -> 32 bytes.

总共占用的内存大小为：
按照上面对字符串类型的分析，desc和name都占用：48 bytes。
所以TestEnum.ONE占用总内存大小为：

> 12(header) + 4(ordinal) + 4(code) + 48 * 2(desc, name) + 4(desc reference) + 4(name reference) = 128 (bytes)

JProfiler中的结果可以验证上述分析： 

![clipboard.png](https://segmentfault.com/img/bVDjFp?w=743&h=259)

### ArrayList

### HashMap

要分析HashMap的内存占用，同样需要先了解HashMap的实现原理。

##### 1. HashMap的数据结构

HashMap是一个“链表散列”的数据结构，即数组和链表的结合体。

![clipboard.png](https://segmentfault.com/img/bVDlSq?w=600&h=357)

从图上可以看出，HashMap底层是一个数组结构，数组中的每一项又是一个链表。当新建一个HashMap的时候，初始化一个数组，源码如下：

```java
    /**
     * The table, initialized on first use, and resized as
     * necessary. When allocated, length is always a power of two.
     * (We also tolerate length zero in some operations to allow
     * bootstrapping mechanics that are currently not needed.)
     */
    transient Node<K,V>[] table;
```

Node是链表中一个结点，一个Node对象保存了一对HashMap的Key,Value以及指向下一个节点的指针，源码如下：

```java
     /**
     * Basic hash bin node, used for most entries.  (See below for
     * TreeNode subclass, and in LinkedHashMap for its Entry subclass.)
     */
    static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        Node<K,V> next;

        Node(int hash, K key, V value, Node<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }
     }
```

##### 2. 构造方法

HashMap提供了四种方式的构造器，分别为指定初始容量及负载因子构造器，指定初始容量构造器，不指定初始容量及负载因子构造器，以及根据已有Map生成新Map的构造器。

```java
/**
     * Constructs an empty <tt>HashMap</tt> with the specified initial
     * capacity and load factor.
     *
     * @  initialCapacity the initial capacity
     * @  loadFactor      the load factor
     * @throws IllegalArgumentException if the initial capacity is negative
     *         or the load factor is nonpositive
     */
    public HashMap(int initialCapacity, float loadFactor) {
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal initial capacity: " +
                                               initialCapacity);
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal load factor: " +
                                               loadFactor);
        this.loadFactor = loadFactor;
        this.threshold = tableSizeFor(initialCapacity);
    }

    /**
     * Constructs an empty <tt>HashMap</tt> with the specified initial
     * capacity and the default load factor (0.75).
     *
     * @  initialCapacity the initial capacity.
     * @throws IllegalArgumentException if the initial capacity is negative.
     */
    public HashMap(int initialCapacity) {
        this(initialCapacity, DEFAULT_LOAD_FACTOR);
    }

    /**
     * Constructs an empty <tt>HashMap</tt> with the default initial capacity
     * (16) and the default load factor (0.75).
     */
    public HashMap() {
        this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
    }

    /**
     * Constructs a new <tt>HashMap</tt> with the same mappings as the
     * specified <tt>Map</tt>.  The <tt>HashMap</tt> is created with
     * default load factor (0.75) and an initial capacity sufficient to
     * hold the mappings in the specified <tt>Map</tt>.
     *
     * @   m the map whose mappings are to be placed in this map
     * @throws  NullPointerException if the specified map is null
     */
    public HashMap(Map<? extends K, ? extends V> m) {
        this.loadFactor = DEFAULT_LOAD_FACTOR;
        putMapEntries(m, false);
    }
```

 如果不指定初始容量及负载因子，默认的初始容量为16， 负载因子为0.75。

负载因子衡量的是一个散列表的空间的使用程度，负载因子越大表示散列表的装填程度越高，反之愈小。对于使用链表法的散列表来说，查找一个元素的平均时间是O(1+a)，因此如果负载因子越大，对空间的利用更充分，然而后果是查找效率的降低；如果负载因子太小，那么散列表的数据将过于稀疏，对空间造成严重浪费。

HashMap有一个容量阈值属性threshold，是根据初始容量和负载因子计算得出threshold=capacity*loadfactor， 如果HashMap中数组元素的个数超过这个阈值，则HashMap会进行扩容。HashMap底层的数组长度总是2的n次方，每次扩容容量为原来的2倍。
扩容的目的是为了减少hash冲突，提高查询效率。而在HashMap数组扩容之后，最消耗性能的点就出现了：原数组中的数据必须重新计算其在新数组中的位置，并放进去，这就是resize。

##### 3. 数据的存储

```java
public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }

    /**
     * Implements Map.put and related methods
     *
     * @ hash hash for key
     * @ key the key
     * @ value the value to put
     * @ onlyIfAbsent if true, don't change existing value
     * @ evict if false, the table is in creation mode.
     * @return previous value, or null if none
     */
    final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        //初始化数组的大小为16，容量阈值为16*0.75=12
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
        //如果key的hash值对应的数组位置没有元素，则新建Node放入此位置
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
        else {
            Node<K,V> e; K k;
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
```

从上面的源代码中可以看出：当我们往HashMap中put元素的时候，先根据key的hashCode重新计算hash值，根据hash值得到这个元素在数组中的位置（即下标），如果数组该位置上已经存放有其他元素了，那么在这个位置上的元素将以链表的形式存放，新加入的放在链头，最先加入的放在链尾。如果数组该位置上没有元素，就直接将该元素放到此数组中的该位置上。

关于HashMap数据的读取这里就不作介绍了。

##### 4. HashMap内存占用

这里分析一个只有一组键值对的HashMap, 结构如下：

```java
    Map<Integer, Integer> testMap = Maps.newHashMap();
    testMap.put(1, 2);
```

首先分析HashMap本身的大小。HashMap对象拥有的属性包括：

```java
/**
     * The table, initialized on first use, and resized as
     * necessary. When allocated, length is always a power of two.
     * (We also tolerate length zero in some operations to allow
     * bootstrapping mechanics that are currently not needed.)
     */
    transient Node<K,V>[] table;

    /**
     * Holds cached entrySet(). Note that AbstractMap fields are used
     * for keySet() and values().
     */
    transient Set<Map.Entry<K,V>> entrySet;

    /**
     * The number of key-value mappings contained in this map.
     */
    transient int size;

    /**
     * The number of times this HashMap has been structurally modified
     * Structural modifications are those that change the number of mappings in
     * the HashMap or otherwise modify its internal structure (e.g.,
     * rehash).  This field is used to make iterators on Collection-views of
     * the HashMap fail-fast.  (See ConcurrentModificationException).
     */
    transient int modCount;

    /**
     * The next size value at which to resize (capacity * load factor).
     *
     * @serial
     */
    // (The javadoc description is true upon serialization.
    // Additionally, if the table array has not been allocated, this
    // field holds the initial array capacity, or zero signifying
    // DEFAULT_INITIAL_CAPACITY.)
    int threshold;

    /**
     * The load factor for the hash table.
     *
     * @serial
     */
    final float loadFactor;
```

HashMap继承了AbstractMap<K,V>, AbstractMap有两个属性：

```java
     transient Set<K>        keySet;
     transient Collection<V> values;
```

所以一个HashMap对象本身的大小为：

> 12(header) + 4(table reference) + 4(entrySet reference) + 4(size) + 4(modCount) + 4(threshold) + 8(loadFactor) + 4(keySet reference) + 4(values reference) = 48(bytes)

接着分析testMap实例在总共占用的内存大小。
根据上面对HashMap原理的介绍，可知每对键值对对应一个Node对象。根据上面的Node的数据结构，一个Node对象的大小为：

> 12(header) + 4(hash reference) + 4(key reference) + 4(value reference) + 4(next pointer reference) = 28 (padding) -> 32(bytes)

加上Key和Value两个Integer对象，一个Node占用内存总大小为：32 + 2 * 16 = 64(bytes)

JProfiler中结果：

![clipboard.png](https://segmentfault.com/img/bVDmzg?w=706&h=249)

下面分析HashMap的Node数组的大小。
根据上面HashMap的原理可知，在不指定容量大小的情况下，HashMap初始容量为16，所以testMap的Node[]占用的内存大小为：

> 16(header) + 16 * 4(Node reference) + 64(Node) = 144(bytes)

JProfile结果：

![clipboard.png](https://segmentfault.com/img/bVDmAh?w=734&h=268)

所以，testMap占用的内存总大小为：

> 48(map itself) + 144(Node[]) = 192(bytes)

JProfile结果：

![clipboard.png](https://static.segmentfault.com/v-5c4ec07f/global/img/squares.svg)

这里只用一个例子说明如何对HashMap进行占用内存大小的计算，根据HashMap初始化容量的大小，以及扩容的影响，HashMap占用内存大小要进行具体分析，
不过思路都是一致的。

### 总结

以上对计算Java对象占用内存的基本规则及方法进行了介绍，并通过分析枚举类，ArrayList, HashMap的内存占用情况介绍了分析复杂对象内存占用的基本方法，
实际工作中需要精确计算Java对象内存占用情况的场景不多，不过作为Java程序员的基本素养，了解这方面内容还是很有必要的。



## [Java之值类型和引用类型的扩展分析](https://www.jianshu.com/p/f6cf438de7f7)

### **大纲：**

#### **1.什么是值类型，什么是引用类型？**

#### 2.值类型int和引用类型Integer的区别和转换

#### 3.Java之按值传递和按引用传递

#### 4.Java的equals和==的区别（联系值类型和引用类型）

#### 5.Java内存为什么有堆栈之分？

### 正文：

#### **1.什么是值类型，什么是引用类型？**

[值类型]: 也就是基本数据类型

八种：   1，整型 byte，short，int，long

2，浮点型  float，double

3，字符型  char

4，逻辑型  boolean

[引用类型]

除了以上八种基本类型外，所有的类型都称为引用类型 如：Interger Double Float String 数组

#### 2.值类型int和引用类型Integer的区别和转换

一个具有值类型（value type）的数据存放在栈内的一个变量中。即是在栈中分配内存空间，直接存储所包含的值，其值就代表数据本身。值类型的数据具有较快的存取速度。

一个具有引用类型（reference type）的数据并不驻留在栈中，而是存储于堆中。即是在堆中分配内存空间，不直接存储所包含的值，而是指向所要存储的值，其值代表的是所指向的地址。当访问一个具有引用类型的数据时，需要到栈中检查变量的内容，该变量引用堆中的一个实际数据。引用类型的数据比值类型的数据具有更大的存储规模和较低的访问速度。



------

int Integer 为例：

int 是基本数据类型，Integer 是一个类（引用类型），是int的扩展，是对int的封装，定义了很多的转换方法。

int a=1;

Integer b=new Integer(a);//jdk1.5之前int转Integer

Integer b=a；//jdk1.5之后int转Integer--拆箱装箱机制

a=b.intValue();//Integer转int

------

拆箱和装箱

装箱（值类型到引用类型）。将一个值类型变量装箱成一个引用类型变量，首先会在托管堆上为新的引用类型变量分配内存空间，然后将值类型变量拷贝到托管堆上新分配的对象内存中，最后返回新分配的对象内存地址。

拆箱（引用类型到值类型）。 获取只想对象中包含值类型部分的指针，然后由程序员手动将其对应的值拷贝给值类型变量。

#### 3.Java之按值传递和按引用传递

在Java里面只有基本类型和按照下面这种定义方式的String是按值传递，其它的都是按引用传递。就是直接使用双引号定义字符串方式：String str = “lingyongqian”;

[值传递]

基本数据类型赋值都属于值传递,传递的是值的拷贝,值传递后,实参传递给形参的值,形参发生改变而不影响实参。即传递后就互不相关。

------

典型例子：

```java
public class Test {

private void test1(int a){

a = 5;

System.out.println("test1方法中的a="+a);

}
public static void main(String[] args) {

Test t = new Test();

int a = 3;
t.test1(a);//这里传递的参数a就是按值传递，传递后，test1方法对变量值的改变不影响这里的a

System.out.println(”main方法中的a=”+a);
    
}

```

 输出结果：

test1方法中的a=5

main方法中的a=3

------

 [引用传递]

引用类型之间赋值属于引用传递。引用传递传递的是对象的引用地址，就是变量所对应的内存空间的地址。也就是它的本身。就是将实参的地址传递给形参，形参改变了，实参当然被改变了，因为他们指向相同的地址。传递前和传递后都指向同一个内存空间。

------

典型例子

```java
public class Test {

private void test1(A a){

a.age = 20;

System.out.println("test1方法中的age="+a.age);

}

public static void main(String[] args) {

Test t = new Test();

A a = new A();

a.age = 10;

t.test1(a);//这里传递的参数a就是按引用传递

System.out.println(”main方法中的age=”+a.age);

}

}

class A{

public int age = 0;

}
```

输出结果：

test1方法中的age=20

main方法中的age=20

 

#### 5.Java内存为什么有堆栈之分？

当一个方法执行时，每个方法都会建立自己的内存栈，在这个方法内定义的变量将会逐个放入这块栈内存里，随着方法的执行结束，这个方法的内存栈也会自然销毁，因此所有在方法中定义的变量都是放在栈内存中。栈的优势是，存取速度比堆要快，仅次于寄存器，栈数据可以共享。但缺点是，存在栈中的数据大小与生存期必须是确定的，缺乏灵活性。

当我们在程序中创建一个对象的时候，这个对象将保存到运行时数据区，以便反复利用（因为对象的创建成本比较大），这个运行时数据区就是堆内存，堆内存中的数据不会随方法的结束而销毁，即使方法结束后，这个对象依然不会被销毁，只有当一个对象没有任何引用变量引用它时，系统的垃圾回收机制才会在合适的时候回收它。



---

## [Java多线程死锁原因及如何避免](https://xinklabi.iteye.com/blog/2020332)

### Java线程死锁

　　Java线程死锁是一个经典的多线程问题，因为不同的线程都在等待那些根本不可能被释放的锁，从而导致所有的工作都无法完成。假设有两个线程，分别代表两个饥饿的人，他们必须共享刀叉并轮流吃饭。他们都需要获得两个锁：共享刀和共享叉的锁。

　　假如线程 “A”获得了刀，而线程“B”获得了叉。线程“A”就会进入阻塞状态来等待获得叉，而线程“B”则阻塞来等待“A”所拥有的刀。这只是人为设计的例子，但尽管在运行时很难探测到，这类情况却时常发生。**虽然要探测或推敲各种情况是非常困难的，但只要按照下面几条规则去设计系统，就能够避免Java线程死锁问题：**

　　**让所有的线程按照同样的顺序获得一组锁。这种方法消除了 X 和 Y 的拥有者分别等待对方的资源的问题。**

 

　　**将多个锁组成一组并放到同一个锁下。前面Java线程死锁的例子中，可以创建一个银器对象的锁。于是在获得刀或叉之前都必须获得这个银器的锁。**

 

　　**将那些不会阻塞的可获得资源用变量标志出来。当某个线程获得银器对象的锁时，就可以通过检查变量来判断是否整个银器集合中的对象锁都可获得。如果是，它就可以获得相关的锁，否则，就要释放掉银器这个锁并稍后再尝试。**

 

　　最重要的是，在编写代码前认真仔细地设计整个系统。多线程是困难的，在开始编程之前详细设计系统能够帮助你避免难以发现Java线程死锁的问题。 

**导致死锁的根源在于不适当地运用“synchronized”关键词来管理线程对特定对象的访问。**“synchronized”关键词的作用是，确保在某个时刻只有一个线程被允许执行特定的代码块，因此，被允许执行的线程首先必须拥有对变量或对象的排他性的访问权。当线程访问对象时，线程会给对象加锁，而这个锁导致其它也想访问同一对象的线程被阻塞，直至第一个线程释放它加在对象上的锁。  

 

##  运算符中优先级

单目运算符：+，-，++，-- 

  算数运算符：+，-，*，/，% 

  移位运算符：<<,>> 

  关系运算符：>,<,>=,<=,==,!= 

  位运算符：&，|，~，^, 

  逻辑运算符：&&，|| 

  三目运算符：表达式1？表达式2：表达式3; 

  赋值运算符：=等 

 

## [多帧滑动窗口和后退N帧协议（GBN) ](https://blog.csdn.net/jxq0816/article/details/52454459)

在后退N帧式ARQ中，发送方不需要在收到上一帧的ACK后才能开始发送下一帧，而是可以连续发送帧。当接受方检测出失序的信息帧后，要求发送方重发最后一个正确接受的信息帧之后的所有未确认的帧；或者当发送方发送了N个帧后，若发现该N个帧的前一个帧在计时器超时后仍未返回其确认信息，则该帧被判为出错或丢失，此时发送方就不得不又重传该出错帧及随后的N个帧。换句话说，接受帧只允许按顺序接受帧。

源站向目的站发送数据帧。当源站发完0号帧后，可以继续发送后续的1号帧、2号帧等。源站每发送一帧就要为该帧设置超时计时器。由于连续发送了许多帧，所以确认帧必须要指明是对哪一帧进行确认。为了减少开销，GBN协议还规定接受端不一定每收到一个正确帧就必须发回一个确认帧，而是可以在连续收到好几个正确的确认帧后，才对最后一个数据帧发确认信息，或者可以在自己有数据要发送时才将对以前正确收到的帧加以捎带确认。这就是说，对某一数据帧的确认就表明该数据帧和这以前所有的数据帧均已正确无误地收到了。

ACK(n+1)表示对第n号帧的确认，表明接受方已正确收到第n帧及以前的所有帧，下一次期望收到第n+1号帧（也可能是第0号帧）。接受端只按序接受数据帧。虽然在有差错的2号帧之后接着又收到了正确的6个数据帧，但接收端必须将这些帧丢弃。接收端虽然丢弃了这些不按序的无出错帧，但应重复发送已经发送过的最后一个确认帧ACK1（这是为了防止已经发送过的确认帧ACK1丢失）。

后退N帧协议的接受窗口为1，可以保证按序接受数据帧。若采用n个比特对帧编号，则其发送窗口的尺寸Wt应满足：1<=Wt<=2^n-1。若发送窗口的尺寸大小2^n-1，则会造成接受方无法分辨新帧和旧帧。

后退N帧协议一方面因连续发送数据帧而提高了信道的利用率，但另一方面，在重传时又必须把原来已发送正确的数据帧进行重传（仅因这些数据帧的前面有一个数据帧出了错），这种做法又使传送速率降低。由此可见，若信道的传输质量很差导致误码率较大时，后退N帧协议不一定优于停止等待协议。

 

## [MYSQL数据库引擎 MYISAM和 INNODB区别](https://www.jianshu.com/p/4792b9fa4415)

MySQL数据库有多种存储引擎：比如：MyISAM、InnoDB、MERGE、MEMORY(HEAP)、BDB(BerkeleyDB)、EXAMPLE、FEDERATED、ARCHIVE、CSV、BLACKHOLE等等，最常见的也就是MyISAM和InnoDB了，下面主要讲解下MyISAM和InnoDB两种mysql数据库存储引擎的区别

 MyISAM引擎是一种非事务性的引擎，提供高速存储和检索，以及全文搜索能力，适合数据仓库等查询频繁的应用。MyISAM在所有MySQL配置里被支持，它是默认的存储引擎，除非你配置MySQL默认使用另外一个引擎。

 MySQL服务器中的其他非事务性存储引擎（如MyISAM）遵从不同的数据完整性范例，称之为“原子操作”。按照事务术语，MyISAM表总能高效地工作在AUTOCOMMIT=1模式下。原子操作通常能提供可比较的完整性以及更好的性能。与经过优化调整的最快的事务性表相比，它的速度快3～5倍。由于MySQL服务器支持两种范例，因而你能决定是否利用原子操作的速度更好地服务于你的应用程序，或使用事务特性。该选择可按表进行。
 　　InnoDB则是一种支持事务的引擎。给MySQL提供了具有提交，回滚和崩溃恢复能力的事务安全（ACID兼容）存储引擎。所以的数据存储在一个或者多个数据文件中，支持类似于Oracle的锁机制。一般在OLTP应用中使用较广泛。如果没有指定InnoDB配置选项，MySQL将在MySQL数据目录下创建一个名为ibdata1的自动扩展数据文件，以及两个名为ib_logfile0和ib_logfile1的日志文件。

##  [mysql 用不到索引的语句](https://blog.csdn.net/bigtree_3721/article/details/72478289)

在mysql中， 以key(a, b, c)为例子：

  　　1.  跳过列，where a = 1 and c = 3，最多用到索引列a；where b = 2 and c = 3，一个也用不到，必须从最左列开始

        　　2.  前面是范围查询，where a = 1 and b > 2 and c = 3，最多用到 a, b两个索引列；
              
                　　3. 顺序颠倒，where c = 3 and b = 2 and a = 1，一个也用不到；

        　　4. 索引列上使用了表达式，如where substr(a, 1, 3) = 'hhh'，where a = a + 1，表达式是一大忌讳，再简单mysql也不认。有时数据量不是大到严重影响速度时，一般可以先查出来，比如先查所有有订单记录的数据，再在程序中去筛选以'cp1001'开头的订单，而不是写sql过滤它；

                　　5. 模糊匹配时，尽量写 where a like 'J%'，字符串放在左边，这样才可能用得到a列索引，甚至可能还用不到，当然这得看数据类型，最好测试一下。

　　排序对索引的影响

　　order by是经常用的语句，排序也遵循最左前缀列的原则，比如key(a, b)，下面语句可以用到（测试为妙）

```sql
select * from tab where a > 1 order by b
select * from tab where a > 1 and b > '2015-12-01 00:00：00' order by b
select * from tab order by a, bs
```


　　以下情况用不到

  　　1. 非最左列，select * from tab order by b;

        　　2. 不按索引列顺序来的，select * from tab where b > '2015-12-01 00:00:00' order by a;

              　　3. 多列排序，但列的顺序方向不一致，select * from tab a asc, b desc。

　　聚簇索引与覆盖索引

　　前面说到，mysql索引从结构上只有两类，BTREE与HASH，覆盖索引只是在查询时，要查询的列刚好与使用的索引列完全一致，mysql直接扫描索引，然后就可返回数据，大大提高效率，因为不需再去原表查询、过滤，这种形式下的索引称作覆盖索引，比如key(a,b)，查询时select a,b from tab where a = 1 and b > 2，本质原因：BTREE索引存储了原表数据。

　　聚簇索引也不是单独的索引，前面简要写到，BTREE索引会把数据放在索引中，即索引的叶子页中，包括主键，主键是跟表数据紧挨着放在一起的，因为表数据只有一份，一列键值要跟每一行数据都紧挨在一起，所以一张表只有一个聚簇索引，对于mysql来说，就是主键列，它是默认的。

　　聚簇索引将表数据组织到了一起（参考前面主键索引简略图），插入时严重依赖主键顺序，最好是连续自增，否则面临频繁页分裂问题，移动许多数据。

 

 

 

 

 

  

 

 

 

 

 

 

 

 

  

 

 

 

  

 

 

 















 

 

 

 

 



