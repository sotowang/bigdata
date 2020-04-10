# [异常体系](https://juejin.im/post/5e8d6b27f265da47c916fc4e)

![img](https://user-gold-cdn.xitu.io/2020/4/8/171586a8ed7d2c92?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

* 除了 `RuntimeException` 和其子类，以及`error`和其子类，其它的所有异常都是 `checkedException`
* Exception 有两种异常，一种是 `RuntimeException` ；一种是 `CheckedException`，**这两种异常都应该去`捕获`。**

## 异常类型

* 受检查的异常(checked)
  * 需要用throws语句在方法或构造函数上声明
  * IO异常及SQL异常
  * **需要catch,不管我们是否愿意,都要写**
* **RuntimeException**（unchecked）
  * 不需在方法或构造函数上声明
  * 可传播到方法或构造函数外面

### [RuntimeException](https://juejin.im/post/5e8d6b27f265da47c916fc4e)

|序号 |	异常名称 |	异常描述|
| ---|--|--|
| 1	 | ArrayIndexOutOfBoundsException|	数组越界异常|
| 2	| NullPointerException | 空指针异常 |
| 3	| IllegalArgumentException|非法参数异常	|
| 4	| NegativeArraySizeException|	数组长度为负异常|
| 5	| IllegalStateException|非法状态异常	|
| 6	| ClassCastException|类型转换异常	|

### Checked Exception

| 序号 | 异常名称               | 异常描述                         |
| ---- | ---------------------- | -------------------------------- |
| 1    | NoSuchFieldException   | 表示该类没有指定名称抛出来的异常 |
| 2    | NoSuchMethodException  | 表示该类没有指定方法抛出来的异常 |
| 3    | IllegalAccessException | 不允许访问某个类的异常           |
| 4    | ClassNotFoundException | 类没有找到抛出异常               |

## [与 Exception 有关的 Java 关键字](https://juejin.im/post/5e8d6b27f265da47c916fc4e)

### throw和throws区别

* 在 Java 中，异常也就是一个对象，它能够被程序员自定义抛出或者应用程序抛出，必须借助于 `throws` 和 `throw` 语句来定义抛出异常。

* throws 和 throw 通常是成对出现的，例如

```java
static void cacheException() throws Exception{

  throw new Exception();

}
```

* throw 语句用在方法体内，表示抛出异常，由方法体内的语句处理。 throws 语句用在方法声明后面，表示再抛出异常，由该方法的调用者来处理。

* throws 主要是声明这个方法会抛出这种类型的异常，使它的调用者知道要捕获这个异常。 throw 是具体向外抛异常的动作，所以它是抛出一个异常实例。

### [try 、finally 、catch](https://juejin.im/post/5e8d6b27f265da47c916fc4e)

* 这三个关键字主要有下面几种组合方式 **try...catch 、try...finally、try...catch...finally**

### [try...with...resources](https://juejin.im/post/5e8d6b27f265da47c916fc4e)

* JDK1.7 使用try...with...resources  优雅关闭资源
* Java 类库中有许多资源需要通过 close 方法进行关闭。比如 InputStream、OutputStream，数据库连接对象 Connection，MyBatis 中的 SqlSession 会话等。作为开发人员经常会忽略掉资源的关闭方法，导致内存泄漏。
* 要使用 try-with-resources 语句，**首先要实现 `AutoCloseable` 接口，此接口包含了单个返回的 close 方法**。Java 类库与三方类库中的许多类和接口，现在都实现或者扩展了 `AutoCloseable `接口。如果编写了一个类，它代表的是必须关闭的资源，那么这个类应该实现 `AutoCloseable `接口。
* java 引入了 try-with-resources 声明，**将 try-catch-finally 简化为 try-catch，这其实是一种`语法糖`，在编译时会进行转化为 try-catch-finally 语句。**

### [异常处理的原则](https://juejin.im/post/5e8d6b27f265da47c916fc4e)

* 不要捕获类似 Exception 之类的异常，而应该捕获类似特定的异常，比如 InterruptedException，方便排查问题，而且也能够让其他人接手你的代码时，会减少骂你的次数。
* 不要生吞异常。这是􏲾异常处理中要特别注重􏱸的事情，因为很可能会􏱓􏱠非常难以􏰰􏰱正常􏳶􏲾结束情况。如果我们不把􏲾异常抛􏲏出来，或者也没有输􏳹出到 􏰩􏱥􏰛Logger􏰜 日志中，程序可能会在后面以不可控的方式结束。
* 不要在函数式编程中使用 checkedException。

## [什么是 Error](https://juejin.im/post/5e8d6b27f265da47c916fc4e)

* Error 是程序无法处理的错误，表示运行应用程序中较严重问题。**大多数错误与代码编写者执行的操作无关，而表示代码运行时 JVM（Java 虚拟机）出现的问题**。这些错误是不可检查的，因为它们在应用程序的控制和处理能力之 外，而且绝大多数是程序运行时不允许出现的状况，比如 `OutOfMemoryError `和 `StackOverflowError`

* 除了程序计数器外，其他区域：方**法区(Method Area)、虚拟机栈(VM Stack)、本地方法栈(Native Method Stack) 和 堆(Heap) 都是可能发生 OutOfMemoryError 的区域**。
  * 虚拟机栈：**如果线程请求的栈深度大于虚拟机栈所允许的深度，将会出现 StackOverflowError 异常**；**如果虚拟机动态扩展无法申请到足够的内存，将出现 OutOfMemoryError**。
  * 本地方法栈和虚拟机栈一样
  * 堆：Java 堆可以处于物理上不连续，逻辑上连续，就像我们的磁盘空间一样，如果堆中没有内存完成实例分配，并且堆无法扩展时，将会抛出 OutOfMemoryError。
  * 方法区：方法区无法满足内存分配需求时，将抛出 OutOfMemoryError 异常。

### [NoClassDefFoundError 和 ClassNotFoundException 有什么区别](https://juejin.im/post/5e8d6b27f265da47c916fc4e)

* 在类的加载过程中， JVM 或者 ClassLoader 无法找到对应的类时，都可能会引起这两种异常/错误，由于不同的 ClassLoader 会从不同的地方加载类，有时是**错误的 CLASSPATH 类路径导致的这类错误**，有时是**某个库的 jar 包缺失引发这类错误**。**NoClassDefFoundError 表示这个类在编译时期存在，但是在运行时却找不到此类**，有时静态初始化块也会导致 `NoClassDefFoundError `错误。

* ClassNotFoundException 与编译时期无关，**当你尝试在运行时使用反射加载类时，ClassNotFoundException 就会出现**

* ClassNotFoundException 和 NoClassDefFoundError 都是由 CLASSPATH 中缺少类引起的，通常是由于缺少 JAR 文件而引起的，但是如果 JVM 认为应用运行时找不到相应的引用，就会抛出 NoClassDefFoundError 错误；当你在代码中显示的加载类比如 `Class.forName()` 调用时却没有找到相应的类，就会抛出 `java.lang.ClassNotFoundException`。

  * **NoClassDefFoundError 是 JVM 引起的错误，是 unchecked，未经检查的。因此不会使用 try-catch 或者 finally 语句块**；另外，**ClassNotFoundException 是受检异常，因此需要 try-catch 语句块或者 try-finally 语句块包围，否则会导致编译错误**。
  * 调用` Class.forName()、ClassLoader.findClass() 和 ClassLoader.loadClass()` 等方法时可能会引起 `java.lang.ClassNotFoundException`，如图所示
  * NoClassDefFoundError 是链接错误，发生在链接阶段，当解析引用找不到对应的类，就会触发；而 ClassNotFoundException 是发生在运行时的异常。

  

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