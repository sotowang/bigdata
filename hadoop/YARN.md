# Yarn与Mesos的对比

## 框架担任的角色：

在Mesos中，各种计算框架是完全融入Mesos中的，也就是说，如果你想在Mesos中添加一个新的计算框架，首先需要在Mesos中部署一套该框架；而在YARN中，各种框架作为client端的library使用，仅仅是你编写的程序的一个库，不需要事先部署一套该框架。从这点上说，YARN运行和使用起来更加方便。

![img](https://upload-images.jianshu.io/upload_images/6116404-e81ad3d9acd16cd2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

# 描述Yarn执行一个任务的过程？

[YARN](<https://blog.csdn.net/weixin_35353187/article/details/84729049>)

1）客户端client向ResouceManager提交Application，ResouceManager接受Application并根据集群资源状况选取一个node来启动Application的任务调度器driver（ApplicationMaster）

2）ResouceManager找到那个node，命令其该node上的nodeManager来启动一个新的JVM进程运行程序的driver（ApplicationMaster）部分，driver（ApplicationMaster）启动时会首先向ResourceManager注册，说明由自己来负责当前程序的运行
3）driver（ApplicationMaster）开始下载相关jar包等各种资源，基于下载的jar等信息决定向ResourceManager申请具体的资源内容。
4）ResouceManager接受到driver（ApplicationMaster）提出的申请后，会最大化的满足
资源分配请求，并发送资源的元数据信息给driver（ApplicationMaster）；
5）driver（ApplicationMaster）收到发过来的资源元数据信息后会根据元数据信息发指令给具体
机器上的NodeManager，让其启动具体的container。
6）NodeManager收到driver发来的指令，启动container，container启动后必须向driver（ApplicationMaster）注册。
7）driver（ApplicationMaster）收到container的注册，开始进行任务的调度和计算，直到
任务完成。

补充：如果ResourceManager第一次没有能够满足driver（ApplicationMaster）的资源请求
，后续发现有空闲的资源，会主动向driver（ApplicationMaster）发送可用资源的元数据信息

以提供更多的资源用于当前程序的运行

# Yarn中的container是由谁负责销毁的，在Hadoop Mapreduce中container可以复用么？

ApplicationMaster负责销毁，在Hadoop Mapreduce不可以复用，在spark on yarn程序container可以复用

 

# 提交任务时，如何指定Spark Application的运行模式？

1）cluster模式：./spark-submit --class xx.xx.xx --master yarn --deploy-mode cluster xx.jar

2) client模式:./spark-submit --class xx.xx.xx --master yarn --deploy-mode client xx.jar

 

#  Spark中的4040端口由什么功能?

收集Spark作业运行的信息



#  谈谈你对container的理解？

1）Container作为资源分配和调度的基本单位，其中封装了的资源如内存，CPU，磁盘，网络带宽等。 目前yarn仅仅封装内存和CPU

2)Container由ApplicationMaster向ResourceManager申请的，由ResouceManager中的资源调度器异步分配给ApplicationMaster

3) Container的运行是由ApplicationMaster向资源所在的NodeManager发起的，Container运行时需提供内部执行的任务命令

 ![167964803287336123.jpg](https://i.loli.net/2019/05/21/5ce3a4eed1bfa51728.jpg)

# Executor启动时，资源通过哪几个参数指定？

1)num-executors是executor的数量
2)executor-memory 是每个executor使用的内存
3)executor-cores 是每个executor分配的CPU

