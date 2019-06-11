# JVM三大性能调优参数

* -Xss 
  * 每个线程的堆栈大小。
* -Xms
  * 堆的初始值
* -Xmx
  * 堆能达到的最大值

# Java内存模型中堆和栈的区别

**内存分配策略**

* 静态存储
  * 编译时确定每个数据目标在运行时的存储空间
* 栈式存储
  * 数据区需求在编译时未知，运行时模块入口前确定
* 堆式存储
  * 编译时或运行时模块入口都无法确定，动态分配

**联系**

* 引用对象，数组时，栈里定义变量保存堆中的目标的首地址

**区别**

* 管理方式
  * 栈自动释放
  * 堆需要GC
* 空间大小
  * 栈比堆小
* 碎片相关
  * 栈产生的碎片远小于堆
* 分配方式
  * 栈支持静态和动态分配
  * 堆仅支持动态分配
* 栈的效率比堆高

# 元空间（metaSpace），堆，线程独占部分间的联系-内存角度

* MetaSpace
  * 类被加载进来时，MetaSpace保存Class对象信息，Method，属性信息
* Java堆
  * Java堆加载的是对象实例（Object），String实例（如果有）
* 线程独占
  * 虚拟机栈
    * 地址引用（String，Object的）
    * 局部变量（a,b,c）
  * 本地栈
  * 程序计数器

# 不同JDK版本之间intern()方法的区别

* JDK6
  * 当调用intern方法时，如果字符串常量池先前已创建出该**字符串对象**，则返回池中的该字符串的引用。否则将此字符串对象添加到字符串常量池中，并且返回该字符串对象的引用。
* JDK6+
  * 当调用intern方法时，如果字符串常量池先前**已创建出该字符串对象**，则返回池中的该字符串的引用。否则，如果该字符串对象已经存在于Java堆中，则将堆中对此**对象的引用添加到字符串常量池**中，并且返回该引用；如果堆中不存在，则在**池中创建该字符串**并返回其引用。



# 线程共享

JVM内存结构](https://mp.weixin.qq.com/s?__biz=MzI4NDY5Mjc1Mg==&mid=2247483949&idx=1&sn=8b69d833bbc805e63d5b2fa7c73655f5&chksm=ebf6da52dc815344add64af6fb78fee439c8c27b539b3c0e87d8f6861c8422144d516ae0a837&scene=21#wechat_redirect)

![](https://mmbiz.qpic.cn/mmbiz_png/PgqYrEEtEnoUSbbnzEiafyyQWUibOfnE3GicpdRQOuxWBrhB3Fic7MRf4z5ywT2RmCicibGibHNQEgUbsibLR1eLVRfo3A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

>堆内存是JVM中最大的一块由年轻代和老年代组成，而年轻代内存又被分成三部分，Eden空间、From Survivor空间、To Survivor空间,默认情况下年轻代按照8:1:1的比例来分配；

方法区存储类信息、常量、静态变量等数据，是线程共享的区域，为与Java堆区分，方法区还有一个别名Non-Heap(非堆)；栈又分为java虚拟机栈和本地方法栈主要用于方法的执行。

在通过一张图来了解如何通过参数来控制各区域的内存大小

![](https://mmbiz.qpic.cn/mmbiz_png/PgqYrEEtEnoUSbbnzEiafyyQWUibOfnE3GQ0NibDSK0gTV91vOLkOGUBM9h9nTHTJ9YfzVonOicI9c1N2cCpG4j5Mg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

控制参数

```markdown
-Xms设置堆的最小空间大小。

-Xmx设置堆的最大空间大小。

-XX:NewSize设置新生代最小空间大小。

-XX:MaxNewSize设置新生代最大空间大小。

-XX:PermSize设置永久代最小空间大小。

-XX:MaxPermSize设置永久代最大空间大小。


```

**没有直接设置老年代的参数，但是可以设置堆空间大小和新生代空间大小两个参数来间接控制。**

>老年代空间大小=堆空间大小-年轻代大空间大小

从更高的一个维度再次来看JVM和系统调用之间的关系

![](https://mmbiz.qpic.cn/mmbiz_png/PgqYrEEtEnoUSbbnzEiafyyQWUibOfnE3G1sZG1aJZSakhFe5d6QeiciaO9ZIDfHrFS9UZx8RfWfkPk9UZLCVdcriaQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



## 堆

**堆（Heap）**：最大一块空间。存放对象实例和数组。全局共享。

Java堆是垃圾收集器管理的主要区域，因此很多时候也被称做“GC堆”。如果从内存回收的角度看，由于现在收集器基本都是**采用的分代收集算法**，	
>所以Java堆中还可以细分为：新生代和老年代；	
>再细致一点的有Eden空间、From Survivor空间、To Survivor空间等。

根据Java虚拟机规范的规定，**Java堆可以处于物理上不连续的内存空间中，只要逻辑上是连续的即可，就像我们的磁盘空间一样**。在实现时，既可以实现成固定大小的，也可以是可扩展的，不过当前主流的虚拟机都是按照可扩展来实现的（通过-Xmx和-Xms控制）。

如果在堆中没有内存完成实例分配，并且堆也无法再扩展时，将会抛出OutOfMemoryError异常。

## 方法区

### MetaSpace（JDK1.8）相比PermGen优势

* 字符串常量池存在永久代中，容易出现性能问题和内存溢出
* 类和方法的信息大小难易确定，给永久代的大小指定带来困难
* 永久代会为GC带来不必要的复杂性
* 方便HotSpot与其他JVM如Jrockit的集成

方法区（Method Area）与Java堆一样，是各个线程共享的内存区域，它用于存储已被虚拟机加载的**类信息、常量、静态变量、即时编译器编译后的代码等数据**。虽然**Java虚拟机规范把方法区描述为堆的一个逻辑部分**，但是它却有一个别名叫做Non-Heap（非堆），目的应该是与Java堆区分开来。

对于习惯在HotSpot虚拟机上开发和部署程序的开发者来说，很多人愿意把方法区称为“**永久代**”（Permanent Generation），本质上两者并不等价，仅仅是因为HotSpot虚拟机的设计团队选择把GC分代收集扩展至方法区，或者说使用**永久代来实现方法区而已**。

Java虚拟机规范对这个区域的限制非常宽松，除了和Java堆一样不需要连续的内存和可以选择固定大小或者可扩展外，还可以选择不实现垃圾收集。相对而言，垃圾收集行为在这个区域是比较少出现的，但并非数据进入了方法区就如永久代的名字一样“永久”存在了。这个区域的内存回收目标主要是针对常量池的回收和对类型的卸载，一般来说这个区域的回收“成绩”比较难以令人满意，尤其是类型的卸载，条件相当苛刻，但是这部分区域的回收确实是有必要的。

根据Java虚拟机规范的规定，当方法区无法满足内存分配需求时，将抛出OutOfMemoryError异常。

![](http://mmbiz.qpic.cn/mmbiz_png/PgqYrEEtEnoL6lB6hGicsicgcT50EBbI0CicvmOXXLKcdAmJVia32ITn6gWezTRCuficFIgibcgDiaBUibjicaQyO7blCyQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

所有的对象在实例化后的整个运行周期内，都被存放在堆内存中。

方法的执行都是伴随着线程的。原始类型的本地变量以及引用都存放在线程栈中。而引用关联的对象比如String，都存在在堆中。为了更好的理解上面这段话，我们可以看一个例子：

```java
import java.text.SimpleDateFormat;
import java.util.Date;
import org.apache.log4j.Logger;
public class HelloWorld {
    private static Logger LOGGER = Logger.getLogger(HelloWorld.class.getName());
 
    public void sayHello(String message) {
        SimpleDateFormat formatter = new SimpleDateFormat("dd.MM.YYYY");
        String today = formatter.format(new Date());
        LOGGER.info(today + ": " + message);
    }
}

```

这段程序的数据在内存中的存放如下：

![](http://mmbiz.qpic.cn/mmbiz_png/PgqYrEEtEnoL6lB6hGicsicgcT50EBbI0CMiazSMwukWpGox7ns74yo5Ke8iaPpD6bXgWnT2T87RFyEoDXAia06P34g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

通过JConsole工具可以查看运行中的Java程序（比如Eclipse）的一些信息：堆内存的分配，线程的数量以及加载的类的个数；

![](http://mmbiz.qpic.cn/mmbiz_png/PgqYrEEtEnoL6lB6hGicsicgcT50EBbI0Cdgw9jArfdDicTUlyicGNQsrE7abLJMlCzAKqDYwK9LfiaLug4UycK3jlQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

# 线程私有

## 程序计数器

* 当前线程所执行的字节码行号指示器
* 改变计数器的值来选取下一条需要执行的字节码指令
* 和线程是一对一的关系即“线程私有”
* 对Java方法计数，如果是Native方法则计数器值为Undefined
* 不会发生内存泄漏

## Java虚拟机栈

* Java方法执行的内存模型
* 包含多个栈帧
  * 局部变量表
    * 方法执行过程中的所有变量
  * 操作数栈
    * 入栈
    * 出栈
    * 复制
    * 交换
    * 产生消费变量
  * 动态连接
  * 方法出口

存放基本型，以及对象引用。

**局部变量表存放了编译期可知的各种基本数据类型**（boolean、byte、char、short、int、float、long、double）**、对象引用**（reference类型，它不等同于对象本身，根据不同的虚拟机实现，它可能是一个指向对象起始地址的引用指针，也可能指向一个代表对象的句柄或者其他与此对象相关的位置）和returnAddress类型（指向了一条字节码指令的地址）。

其中64位长度的long和double类型的数据会占用2个局部变量空间（Slot），其余的数据类型只占用1个。局部变量表所需的内存空间在编译期间完成分配，当进入一个方法时，这个方法需要在帧中分配多大的局部变量空间是完全确定的，在方法运行期间不会改变局部变量表的大小。

在Java虚拟机规范中，对这个区域规定了两种异常状况：

```
如果线程请求的栈深度大于虚拟机所允许的深度，将抛出StackOverflowError异常；
如果虚拟机栈可以动态扩展（当前大部分的Java虚拟机都可动态扩展，只不过Java虚拟机规范中也允许固定长度的虚拟机栈），
当扩展时无法申请到足够的内存时会抛出OutOfMemoryError异常。
```

## 本地方法栈
本地方法栈（Native Method Stacks）与虚拟机栈所发挥的作用是非常相似的，其区别不过是虚拟机栈为虚拟机执行Java方法（也就是字节码）服务，而本地方法栈则是为虚拟机使用到的Native方法服务**。虚拟机规范中对本地方法栈中的方法使用的语言、使用方式与数据结构并没有强制规定**，因此具体的虚拟机可以自由实现它。甚至有的虚拟机（譬如Sun HotSpot虚拟机）直接就把本地方法栈和虚拟机栈合二为一。
>与虚拟机栈一样，本地方法栈区域也会抛出StackOverflowError和OutOfMemoryError异常。



# JMM中的主内存和工作内存

* **JMM与Java内存区域划分是不同的概念层次**
  * **JMM描述的是一组规则,围绕原子性,有序性,可见性展开**
  * 相似点
    * 存在共享区域和私有区域
* 主内存与工作内存的数据存储类型以及操作方式归纳
  * 方法里的基本数据类型本地变量将直接存储在工作内存的栈帧结构中
  * 引用类型的本地变量
    * 引用存储在工作内存中,实际存储在主内存中
    * 成员变量,static变量,类信息均会被存储在主内存中
    * 主内存共享的方式是线程各拷贝一份数据到工作内存,操作完成后刷新回主内存

# JMM如何解决可见性

* 指令重排序需要满足的条件
  * 在单线程环境下不能改变程序运行的结果
  * 存在数据依赖关系的不允许重排序
  * 即 **无法通过happens-before原则(八大原则)推导出来的才能进行指令的重排序**
    * A操作的结果需要对B操作可见,则A与B存在happens-before关系
* volatile: JVM提供的轻量级同步机制
  * 保证被volatile修饰的共享变量对所有线程总是可见的
* 禁止指令重排序优化
  * volatile如何禁止重排优化