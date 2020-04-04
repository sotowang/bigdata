# 异常类型

* 受检查的异常(checked)
  * 需要用throws语句在方法或构造函数上声明
* 不受检查的异常（unchecked）
  * 不需在方法或构造函数上声明
  * 可传播到方法或构造函数外面

# 异常机制

- 运行时异常（unchecked）
  - 由JVM接管,比如空指针异常
- 检查式异常（checked exception）
  - IO异常及SQL异常
  - 需要catch,不管我们是否愿意,都要写

# throw和throws区别

* throw关键字用来在程序中明确地抛出异常
* throws来表明方法不能处理的异常，多个异常用逗号分隔

# Throwable

* 是所有错误和异常的超类
* 2个子类
  * Error
  * Exception

## Error和Exception的区别

* 父类都为Throwable
* Error：程序无法处理的系统错误，编译器不做检查,JVM承担的责任,大部分会终止线程
* Exception：程序可以处理的异常，捕获后可能恢复
  * unCheckedException
    * 不可预知的，程序应当自行避免，由程序承担责任
      * 空指针异常，数组下标越界
  * CheckedException可检查异常是Java编译器应该负担的责任

# RuntimeException

* NullPointerException -空指针引用异常
* ClassCastException -类型强制转换异常
* IllegalArgumentException - 传递非法参数异常
* IndexOutOfBoundsException - 下标越界异常
* NumberFormatException - 数字格式异常 

# Checked Exception

* ClassNotFoundException - 找不到指定class异常
* IOException - IO操作异常

# Error

* `NoClassDefFoundError`
  * 类依赖的class或者jar不存在
  * 类文件存在，但是存在不同的域中
  * 大小写问题，javac编译的时候是无视大小写的，很有可能编译出的class文件与想要的不一样
* `StackOverflowError` 
* `OutOfMemoryError` 
  * `Java heap space`
  * `GC overhead limit exceeded`
    * 超过98%的时间用来做GC并且回收不到2%内存
  * `Direct buffer memory`
    * 堆内存充足，本地内存用尽；因堆内存使用少，不需要GC，`DirectByteBuffer`对象就不会被回收
  * `unable to create new native thread`
    * 原因
      * 一个应用进程创建多个线程，超过系统极限（Linux默认1024）
    * 解决
      * 降低自身应用线程数
      * 修改linux服务器配置
  * `Metaspace`

# Java的异常处理机制

* 抛出异常
  * 创建异常对象，交由运行时系统处理
* 捕获异常
  * 寻找合适的异常处理器处理异常，否则终止运行

# 异常处理框架

在用户看来，应用系统发生的所有异常都是应用系统内部的异常

* 设计一个通用的继承自RuntimeException的异常来统一处理
* 其余异常都统一转译为上述异常AppException
* 在catch之后，抛出上述异常的子类，并提供足以定位的信息
* 由前端接收AppException做统一处理

# [JVM OOM排查](https://www.jianshu.com/p/91e2b102dd4c)

## 一、jdk 自带基础工具

1.  jps(Java Virtual Machine Process Status Tool) 

   实际中这是最常用的命令，下面要介绍的小工具更多的都是**先要使用jps查看出当前有哪些Java进程，获取该Java进程的id后再对该进程进行处理**。

2.  **jstack**

   * jstack 主要用来查看某个Java进程内的线程堆栈信息

   * ```
     option 说明如下：
     
     -l，会打印出额外的锁信息，在发生死锁时可以用jstack -l pid来观察锁持有情况
     
     -m 不仅会输出Java堆栈信息，还会输出C/C++堆栈信息（比如Native方法）
     ```

   * `jstack -l  pid `

     ![img](https://upload-images.jianshu.io/upload_images/14461475-42da5ed62aef45a8.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

3. jmap（Memory Map）

   > ​     **jmap导出堆内存，然后使用jhat来进行分析**

```
-heap：打印jvm heap的情况
-histo：打印jvm heap的直方图。其输出信息包括类名，对象数量，对象占用大小。

-histo：live ：同上，但是只打印存活对象的情况

-permstat：打印permanent generation heap情况（持久代）

输出 类加载器名称、对象是否存活、对象地址、父类加载器、已加载的类大小等信息

-dump:<dump-options>    打印 dump（内存使用） 日志文件 
```

* **jmap -heap pid**

  **查看进程堆内存使用情况:****包括使用的GC算法、堆配置参数和各代中堆内存使用

* **jmap -histo[:live] pid**

  **查看堆内存中的对象数目、大小统计直方图，如果带上live则只统计活对象**

## 二、MAT 分析Dump

```
Dump是反应Java堆使用情况的内存镜像，其中主要包括系统信息、虚拟机属性、完整的线程Dump、所有类和对象的状态等。 一般，在内存不足、GC异常等情况下，我们就会怀疑有内存泄漏。这个时候我们就可以制作堆Dump来查看具体情况。分析原因。
```

* Dump 文件的分析有多种方式

  > ​      1、命令jhat 分析（麻烦）
  >
  > ​      2、**VisualVM** 工具分析 
  >
  > ​      3、MAT 分析

## [内存溢出[OOM]异常快速定位排查](https://xiaolong.li/2019/09/27/OOM-error/)

# [CPU负载过高异常定位排查](https://xiaolong.li/2019/09/26/CPU-loading-hight-exception/)

## 什么样的场景会导致线上CPU负载过高？

代码层面常见的场景有：

> 程序陷入死循环，不停地消耗CPU
> 线程死锁，线程相互等待，导致假死状态，不停地消耗CPU



## 定位过程

### top查找占用CPU高的进程

* 查看进程中最耗cpu的子线程

```
top -Hp 4369
```

如下图：可看出PID为 **4372** 的线程占用cpu最高，达到了99.7%
[![image](https://xiaolong.li/images/exception/2019-09-26-top-Hp.png)](https://xiaolong.li/images/exception/2019-09-26-top-Hp.png)

* ##### 线程id转换为16进制

  ```
  printf "%x \n" 4372
  ```

  得到16进制数 **1114** 。

### 定位问题代码

使用jstack查看进程堆栈信息，定位占cpu最高的线程对应问题代码。

> jstack 4369 | grep 1114 -A 30