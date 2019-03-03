### JDK（Java开发工具集）  JRE（Java Runtime Environment） JVM（Java Virtual Machine） 关系
> JDK包含JRE  JRE包含JVM

![DeepinScreenshot_select-area_20190113201905.png](https://i.loli.net/2019/01/13/5c3b2ccc94278.png)

#### 内存溢出问题的解决
> -XX:+HeapDumpOnOutOfMemoryError

```
如果发生了内存泄露，好重现的话打开JVM的HeapDumpOnOutOfMemoryError参数，待到有了Heap Dump再使用MAT等工具分析。
```

#### JVM监控工具
> jconsole

[JVM内存区域详解（Eden Space、Survivor Space、Old Gen、Code Cache和Perm Gen）](https://blog.csdn.net/shiyong1949/article/details/52585256)

#### Java虚拟机
* Sun Classic VM(第一个Java虚拟机)   --->已过时
* Exact VM  -->准确试内存管理
* HotSpot VM  
* KVM  -->在手机平台运行

#### Java虚拟机内存管理
![Java虚拟机内存管理](https://images2017.cnblogs.com/blog/1072930/201712/1072930-20171229170534570-429539433.png)
[https://www.cnblogs.com/shyroke/p/8146608.html](https://www.cnblogs.com/shyroke/p/8146608.html)

##### 线程共享区	
Java 堆

```
Java堆是Java虚拟机所管理的内存中最大的一块，在虚拟机启动时创建，

其唯一目的就是存放对象实例：所有的对象实例以及数组都要在堆上分配
（但随着JIT编译器的发展与逃逸分析技术逐渐成熟，所有的对象分配在堆上也渐渐不是那么绝对了）。

Java堆是垃圾收集器管理的主要区域，现在收集器基本采用分代收集算法，

所以Java堆还可细分为：新生代和老年代；

再细致点分为Eden空间，From Survivor空间，To Survivor空间等。

根据Java虚拟机规范的规定，Java堆可以处于物理上不连续的内存空间中，只要逻辑上连续即可。

如果堆中没有内存完成实例分配，并且堆也无法再扩展时，将会抛出OutOfMemoryError异常。
```

方法区

```
用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。

所谓的方法区为永久代（Permanent Generation)的说法，仅仅是因为HotSpot虚拟机将GC分代收集扩展至方法区，或者说使用永久代来实现方法区而已。

对于其他虚拟机是不存在永久代的说法的。

```

运行时常量池（Runtime Constant Pool）

```
运行时常量池是方法区的一部分，用于存放编译器生成的各种字面量和符号引用，一般还会存放翻译出来的直接引用。

这部分内容将在类加载后进入方法区的运行时常量池中存放。

当常量池无法再申请到内存时抛出OutOfMemoryError异常。
```

##### 线程独占区	

线程独占区之程序计数器
    
```
如果线程正在执行的是一个Java方法，这个计数器记录的是正在执行的虚拟机字节码指令的地址；

如果正在执行的是Native方法，这个计数器值则为空（Undefined）。

此内存区域是唯一一个在Java虚拟机规范中没有规定任何OutOfMemoryError情况的区域
```

Java虚拟机栈

```
Java虚拟机栈与线程生命周期相同。

其描述的是Java方法执行的内存模型：

每个方法在执行的同时都会创建一个栈帧（Stack Frame)用于存储局部变量表、操作数栈、动态链接、方法出口等信息。
        
每一个方法从调用直至执行完成的过程，就对应着一个栈帧在虚拟机栈中从入栈到出栈的过程。
```

局部变量表

```
局部变量表存放了编译器可知的各种基本数据类型（boolean, byte, char, short, int, float, long, double)，
对象引用（reference类型）和returnAddress类型（指向了一条字节码指令的地址）。
```

在这个区域中，Java虚拟机规范规定了两种异常情况：

```
如果线程请求的栈深度大于虚拟机所允许的深度，抛出StackOverflowError异常；	
如果虚拟机栈可以动态扩展
（当前大部分Java虚拟机都可动态扩展，只不过Java虚拟机规范中也允许固定长度的虚拟机栈），
并且扩展时无法申请到足够的内存，就会抛出OutOfMemoryError异常。
```

本地方法栈

```
本地方法栈与虚拟机栈所发挥的作用非常相似，
区别是：虚拟机栈为虚拟机执行Java方法（也就是字节码）服务，
		本地方法栈为虚拟机使用到的Native方法服务。

由于虚拟机规范中没有对本地方法栈中的语言、使用方式与数据结构进行强制规定，
有的虚拟机（如Sun HotSpot虚拟机）直接把本地方法栈和虚拟机栈合二为一。
本地方法栈也会抛出StackOverflowError异常和OutOfMemoryError异常。
```

##### 直接内存（Direct Memory）

```
直接内存并不是虚拟机运行时数据区的一部分，也不是虚拟机规范中定义的内存区域，但是也会导致OutOfMemoryError异常出现。

例如在JDK1.4中新加入的NIO(New Input/Output)类，引入了一种基于通道（Channel）与缓冲区（Buffer）的I/O方式，
他可以使用Native函数库直接分配堆外内存，
然后通过一个存储在Java堆中的DirectByteBuffer对象作为这块内存的引用进行操作。
这样能在一些场景中显著提高性能，因为避免了在Java堆和Native堆中来回复制数据。

本机直接内存不会受到Java堆大小的限制，但是会受到本机总内存（包括RAM和SWAP区或者分页文件）大小以及处理器寻址空间的限制。
动态扩展时出现OutOfMemoryError异常。
```

---

#### 【深入理解JVM】：Java对象的创建、内存布局、访问定位

[java对象的结构和对象的访问定位](https://www.cnblogs.com/shyroke/p/8159094.html)	

[Java对象的创建、内存布局、访问定位](https://blog.csdn.net/u011080472/article/details/51321769)

##### 对象的创建

```
一个简单的创建对象语句Clazz instance = new Clazz();
包含的主要过程包括了类加载检查、对象分配内存、并发处理、内存空间初始化、对象设置、执行ini方法等。
```

![主要流程如下](https://img-blog.csdn.net/20160505123459787)	

1  类加载检查

```
JVM遇到一条new指令时，
首先检查这个指令的参数是否能在常量池中定位到一个类的符号引用，
并且检查这个符号引用代表的类是否已被加载、解析和初始化过。
如果没有，那必须先执行相应的类的加载过程。
```

2  对象分配内存

```
对象所需内存的大小在类加载完成后便完全确定（对象内存布局），
为对象分配空间的任务等同于把一块确定大小的内存从Java堆中划分出来。

根据Java堆中是否规整有两种内存的分配方式：（Java堆是否规整由所采用的垃圾收集器是否带有压缩整理功能决定）
```

指针碰撞(Bump the pointer) 

```
Java堆中的内存是规整的，
所有用过的内存都放在一边，空闲的内存放在另一边，中间放着一个指针作为分界点的指示器，
分配内存也就是把指针向空闲空间那边移动一段与内存大小相等的距离。
例如：Serial、ParNew等收集器。
```

空闲列表(Free List) 

```
Java堆中的内存不是规整的，
已使用的内存和空闲的内存相互交错，就没有办法简单的进行指针碰撞了
。虚拟机必须维护一张列表，记录哪些内存块是可用的，
在分配的时候从列表中找到一块足够大的空间划分给对象实例，并更新列表上的记录。
例如：CMS这种基于Mark-Sweep算法的收集器。
```

3 并发处理

```
对象创建在虚拟机中是非常频繁的行为，
即使是仅仅修改一个指针指向的位置，在并发情况下也并不是线程安全的，
可能出现正在给对象A分配内存，指针还没来得及修改，对象B又同时使用了原来的指针来分配内存的情况。
```

同步 

```
虚拟机采用CAS配上失败重试的方式保证更新操作的原子性
```

本地线程分配缓冲（Thread Local Allocation Buffer, TLAB） 

```
把内存分配的动作按照线程划分为在不同的空间之中进行，
即每个线程在Java堆中预先分配一小块内存（TLAB）。
哪个线程要分配内存，就在哪个线程的TLAB上分配。
只有TLAB用完并分配新的TLAB时，才需要同步锁定。
```

4.内存空间初始化

```
虚拟机将分配到的内存空间都初始化为零值（不包括对象头）,如果使用了TLAB，这一工作过程也可以提前至TLAB分配时进行。

内存空间初始化保证了对象的实例字段在Java代码中可以不赋初始值就直接使用，程序能访问到这些字段的数据类型所对应的零值。

注意：类的成员变量可以不显示地初始化（Java虚拟机都会先自动给它初始化为默认值）。
方法中的局部变量如果只负责接收一个表达式的值，可以不初始化，但是参与运算和直接输出等其它情况的局部变量需要初始化。
```

5.对象设置

```
虚拟机对对象进行必要的设置，
例如这个对象是哪个类的实例、如何才能找到类的元数据信息、对象的哈希码、对象的GC分代年龄等信息。
这些信息存放在对象的对象头之中。
```

6 执行init()

```
在上面的工作都完成之后，从虚拟机的角度看，一个新的对象已经产生了。
但是从Java程序的角度看，对象的创建才刚刚开始
init()方法还没有执行，所有的字段都还是零。

所以，一般来说（由字节码中是否跟随invokespecial指令所决定），
执行new指令之后会接着执行init()方法，把对象按照程序员的意愿进行初始化，
这样一个真正可用的对象才算产生出来。
```

##### 对象内存布局

在HotSpot虚拟机中，对象在内存中存储的布局可以分为3块区域：
		对象头(Header)、实例数据(Instance Data)和对齐填充(Padding)。

1.对象头	

```
HotSpot虚拟机的对象头包括两部分信息：运行时数据(哈希值  GC分代年龄)和类型指针。
```

* 运行时数据	

```
用于存储对象自身的运行时数据，如哈希码（HashCode）、GC分代年龄、锁状态标志、线程持有的锁、偏向线程ID、偏向时间戳等。
```

![](https://img-blog.csdn.net/20160505124345019)

* 类型指针

```
即对象指向它的类元数据的指针，虚拟机通过这个指针来确定这个对象是哪个类的实例。

如果对象是一个Java数组，那在对象头中还必须有一块用于记录数组长度的数据，
因为虚拟机可以通过普通Java对象的元数据信息确定Java对象的大小，但是从数组的元数据中无法确定数组的大小。 
(并不是所有的虚拟机实现都必须在对象数据上保留类型指针，
换句话说，查找对象的元数据并不一定要经过对象本身，可参考对象的访问定位)
```

2.实例数据

```
实例数据部分是对象真正存储的有效信息，也是在程序代码中所定义的各种类型的字段内容。
无论是从父类中继承下来的，还是在子类中定义的，都需要记录下来。
HotSpot虚拟机默认的分配策略为longs/doubles、ints、shorts/chars、bytes/booleans、oop，
从分配策略中可以看出，相同宽度的字段总是分配到一起。
```

3.对齐填充

```
HotSpot虚拟机要求对象的起始地址必须是8字节的整数倍，
也就是对象的大小必须是8字节的整数倍。
而对象头部分正好是8字节的倍数（1倍或者2倍），
因此，当对象实例数据部分没有对齐的时候，就需要通过对齐填充来补全。
```

#### 对象访问定位

```
Java程序需要通过栈上的引用数据来操作堆上的具体对象。
对象的访问方式取决于虚拟机实现，
目前主流的访问方式有使用句柄和直接指针两种。
```

```
句柄，可以理解为指向指针的指针，维护指向对象的指针变化，而对象的句柄本身不发生变化；
指针，指向对象，代表对象的内存地址。
```

句柄 

```
Java堆中划分出一块内存来作为句柄池，引用中存储对象的句柄地址，而句柄中包含了对象实例数据与类型数据各自的具体地址信息。
```

![](https://img-blog.csdn.net/20160505130804767)

优势：引用中存储的是稳定的句柄地址，在对象被移动(垃圾收集时移动对象是非常普遍的行为)时只会改变句柄中的实例数据指针，而引用本身不需要修改。

直接指针

```
如果使用直接指针访问，那么Java堆对象的布局中就必须考虑如何放置访问类型数据的相关信息，而引用中存储的直接就是对象地址。
```
![](https://img-blog.csdn.net/20160505130823283)

优势：速度更快，节省了一次指针定位的时间开销。由于对象的访问在Java中非常频繁，因此这类开销积少成多后也是非常可观的执行成本。（例如HotSpot）

---

### 垃圾回收

[垃圾回收之判定垃圾对象](https://www.cnblogs.com/shyroke/p/8159243.html)

#####  判定垃圾对象之引用计数法

* 引用计数器算法算是一种古老的java垃圾回收算法，目前很多版本的java（jdk1.8也已废弃）已经废弃掉这种算法了。

>定义：给每个对象分配一个计数器，当有引用指向这个对象时，计数器加1，当指向该对象的引用失效时，计数器减一。最后如果该对象的计算器为0时，java垃圾回收器会认为该对象是可回收的。	

缺陷：
* 每次对象被引用时，都需要去更新计数器，有一点时间开销。
* 无法解决循环引用问题。
* 浪费cpu，即使内存够用，仍然在运行时进行计数器的统计

什么是循环引用问题

```
public class GCTest {

    private Object instance;

    public static void main(String[] args) {

        GCTest gc1 = new GCTest();
        GCTest gc2 = new GCTest();

        gc1.instance = gc2;
        gc2.instance = gc1;

        gc1 = null;
        gc2 = null;

        System.gc();

    }

}
```

如上述代码，在gc1 = null; gc2 = null; 这两条语句还没执行的时候，如下图，两个对象的计数器值都是2

![](https://images2017.cnblogs.com/blog/1072930/201712/1072930-20171231231126054-1025406549.png)	

但是当gc1 = null; gc2 = null; 执行后如下图，两个对象的计数器值都是1，所以对象不会被清理，但是这两个对象其实是垃圾了，这就是循环引用问题。	

![](https://images2017.cnblogs.com/blog/1072930/201712/1072930-20171231231426601-602197925.png)

##### 判定垃圾对象之可达性分析算法
这个算法的基本思路就是通过一系列的称谓“GC Roots"的对象作为起始点，从这些节点开始向下搜索，搜索所有走过的路径为引用链，当一个对象到GC Roots没有任何引用链相连时，则证明此对象时不可用的：
 
注：Java语言中，可作为GC Roots的对象包括下面几种：

```
1) 虚拟机栈(栈帧中的本地变量表)中引用的对象

2) 方法区中类静态属性引用的对象

3) 方法区中常量引用的对象

4) 本地方法栈中JNI(即一般说的Native方法)引用的对象
```

![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180101000505460-1654287575.png)

如上图，以栈中的引用为GC ROOTS，对象3和对象4随便彼此引用，但是GC ROOTS无法搜索到，所以对象3和对象4会被当成垃圾清理。

---

### 垃圾回收算法

[垃圾回收算法](https://www.cnblogs.com/shyroke/p/8167777.html)

#### 标记-清除算法

```
最基础的收集算法是“标记-清除”（Mark-Sweep）算法，
如它的名字一样，算法分为“标记”和“清除”两个阶段：
首先标记出所有需要回收的对象，在标记完成后统一回收掉所有被标记的对象，	
它的标记过程其实在前一节讲述对象标记判定时已经基本介绍过了。
之所以说它是最基础的收集算法，是因为后续的收集算法都是基于这种思路并对其缺点进行改进而得到的。
```

```
它的主要缺点有两个：一个是效率问题，标记和清除过程的效率都不高；
另外一个是空间问题，标记清除之后会产生大量不连续的内存碎片，
空间碎片太多可能会导致，当程序在以后的运行过程中需要分配较大对象时无法找到足够的连续内存而不得不提前触发另一次垃圾收集动作。 
```
标记-清除算法的执行过程如图	

![](https://i.loli.net/2019/01/14/5c3c5bff5b1ed.jpeg)

##### 复制算法
为了解决效率问题，一种称为“复制”（Copying）的收集算法出现了，它将可用内存按容量划分为大小相等的两块，每次只使用其中的一块。当这一块的内存用完了，就将还存活着的对象复制到另外一块上面，然后再把已使用过的内存空间一次清理掉。这样使得每次都是对其中的一块进行内存回收，内存分配时也就不用考虑内存碎片等复杂情况，只要移动堆顶指针，按顺序分配内存即可，实现简单，运行高效。

>只是这种算法的代价是将内存缩小为原来的一半，未免太高了一点。

复制算法的执行过程如图		

![20141102170015242.jpeg](https://i.loli.net/2019/01/14/5c3c5cb774836.jpeg)		

```
 事实上，现在商用的虚拟机都采用这种算法来回收新生代。
 因为研究发现，新生代中的对象每次回收都基本上只有10%左右的对象存活，所以需要复制的对象很少，效率还不错。
 正如在博文《JVM 内存模型概述》中介绍的那样，
 实践中会将新生代内存分为一块较大的Eden空间和两块较小的Survivor空间 (如下图所示)，每次使用Eden和其中一块Survivor。
 当回收时，将Eden和Survivor中还存活着的对象一次地复制到另外一块Survivor空间上，
 最后清理掉Eden和刚才用过的Survivor空间。
 HotSpot虚拟机默认Eden和Survivor的大小比例是 8:1，也就是每次新生代中可用内存空间为整个新生代容量的90% ( 80%+10% )，
 只有10% 的内存会被“浪费”。
```

![](http://static.zybuluo.com/Rico123/pml3jw51zgc8mqffpwt6793t/heap.bmp)

##### 标记-整理算法
复制收集算法在对象存活率较高时就要执行较多的复制操作，效率将会变低。	
更关键的是，如果不想浪费50%的空间，就需要有额外的空间进行分配担保，以应对被使用的内存中所有对象都100%存活的极端情况，所以在老年代一般不能直接选用这种算法。	
根据老年代的特点，有人提出了另外一种“标记-整理”（Mark-Compact）算法，	
标记过程仍然与“标记-清除”算法一样，但后续步骤不是直接对可回收对象进行清理，	
而是让所有存活的对象都向一端移动，然后直接清理掉端边界以外的内存，	
“标记-整理”算法的示意图如图	

![20141102170015242.jpeg](https://i.loli.net/2019/01/14/5c3c6e58a317e.jpeg)

##### 分代收集算法
当前商业虚拟机的垃圾收集都采用“分代收集”（Generational Collection）算法，	
这种算法并没有什么新的思想，只是根据对象的存活周期的不同将内存划分为几块。	
一般是把Java堆分为新生代和老年代，这样就可以根据各个年代的特点采用最适当的收集算法。	
在新生代中，每次垃圾收集时都发现有大批对象死去，只有少量存活，那就选用复制算法，只需要付出少量存活对象的复制成本就可以完成收集。	
而老年代中因为对象存活率高、没有额外空间对它进行分配担保，就必须使用“标记-清理”或“标记-整理”算法来进行回收

---

### 垃圾收集器

[垃圾收集器](https://www.cnblogs.com/shyroke/p/8168057.html)

##### 垃圾收集器之Serial
> serial收集器是最基本发展最悠久的收集器。	
> serial收集器是一个单线程的收集器，但他的“单线程”的意义并不仅仅只会使用一个cpu或者一条线程去完成垃圾收集工作，更重要的是在他进行垃圾收集时，必须暂停其他所有的工作线程，直到收集结束。


serial/serial Old收集器运行示意图	

![20141102170015242.jpeg](https://i.loli.net/2019/01/14/5c3c7118d420d.jpeg)	


serial的优点：

```
简单有效(与其他收集器的单线程比)，
对于限定单个cpu的环境来说，serial收集器由于没有线程交互的开销，专心做垃圾收集自然可以获得最高的单线程的收集效率。
serial收集期对于运行在client模式下的虚拟机来说是一个很好的选择。
串行收集器是最古老，最稳定以及效率高的收集器，可能会产生较长的停顿，只使用一个线程去回收。
新生代、老年代使用串行回收；新生代复制算法、老年代标记-压缩；垃圾收集的过程中会Stop The World（服务暂停）
串行垃圾回收器通过持有应用程序所有的线程进行工作。
它为单线程环境设计，只使用一个单独的线程进行垃圾回收，通过冻结所有应用程序线程进行工作，所以可能不适合服务器环境。
它最适合的是简单的命令行程序。
```

* 通过JVM参数-XX:+UseSerialGC可以使用串行垃圾回收器。

##### 垃圾收集器之ParNew收集器
>ParNew收集器是JAVA虚拟机中垃圾收集器的一种。	
它是Serial收集器的多线程版本，除了使用多条线程进行垃圾收集之外，其余行为包括Serial收集器可用的所有控制参数	
（例如：-XX:SurvivorRatio、 -XX:PretenureSizeThreshold、-XX:HandlePromotionFailure等）、收集算法、Stop The World、对象分配规则、回收策略等都与Serial收集器一致。	
ParNew是许多运行在Server模式下的虚拟机中首选的新生代收集器，除了Serial收集器外，只有它能与CMS收集器配合工作。	
ParNew收集器其实就是Serial收集器的多线程版本。新生代并行，老年代串行；新生代复制算法、老年代标记-压缩

参数控制：
-XX:+UseParNewGC  ParNew收集器
-XX:ParallelGCThreads 限制线程数量

##### 垃圾收集器之parallel 收集器
> Parallel Scavenge收集器也是使用复制算法的收集器 	
Parallel Scavenge的特点：	
	Parallel Scavenge关注与其他收集器不同，CMS等收集器的关注点是尽可能地缩短垃圾收集时用户线程的停顿时间，而Parallel Scavenge收集器的目标则是达到一个可控制的吞吐量，	
    所谓的吞吐量就是CPU用于运行用户代码的时间与CPU总耗时间的比值，即吞吐量 = 运行用户代码时间/(运行用户代码时间+垃圾收集时间)。
    
![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180101182807174-1429867127.png)	

参数控制：	

```
控制最大垃圾收集停顿时间： -XX:MaxGCPauseMills
直接设置吞吐量大小： -XX:GCTimeRatio   （0,100）
自动调整新生代大小（-Xmm）、
Eden与Survivor区比例（-XX:SurvivorRatio）、
晋升老年代对象年龄（-XX：PretenureSizeThreshold）:-XX:+UseAdaptiveSizePolicy
```

#### 垃圾收集器之CMS（Concurrent Mark Sweep）收集器  （收集老年代）

* CMS（Concurrent Mark Sweep）收集器是一种以获取最短回收停顿时间为目标的收集器。
* 目前很大一部分的Java应用都集中在互联网站或B/S系统的服务端上，
* 这类应用尤其重视服务的响应速度，希望系统停顿时间最短，以给用户带来较好的体验

从名字（包含“Mark Sweep”）上就可以看出CMS收集器是基于“标记-清除”算法实现的，它的运作过程相对于前面几种收集器来说要更复杂一些，整个过程分为4个步骤，包括： 

```
初始标记（CMS initial mark）
并发标记（CMS concurrent mark）
重新标记（CMS remark）
并发清除（CMS concurrent sweep）
```

```
 其中初始标记、重新标记这两个步骤仍然需要“Stop The World”。	
 初始标记仅仅只是标记一下GC Roots能直接关联到的对象，速度很快，
 并发标记阶段就是进行GC Roots Tracing的过程，
 而重新标记阶段则是为了修正并发标记期间，因用户程序继续运作而导致标记产生变动的那一部分对象的标记记录，
 这个阶段的停顿时间一般会比初始标记阶段稍长一些，但远比并发标记的时间短。 
由于整个过程中耗时最长的并发标记和并发清除过程中，
收集器线程都可以与用户线程一起工作，所以总体上来说，CMS收集器的内存回收过程是与用户线程一起并发地执行。
```

* 优点:并发收集、低停顿 
* 缺点：产生大量空间碎片、并发阶段会降低吞吐量
   
参数控制：
   
   ```
-XX:+UseConcMarkSweepGC  使用CMS收集器

-XX:+ UseCMSCompactAtFullCollection Full GC后，进行一次碎片整理；整理过程是独占的，会引起停顿时间变长

-XX:+CMSFullGCsBeforeCompaction  设置进行几次Full GC后，进行一次碎片整理

-XX:ParallelCMSThreads  设定CMS的线程数量（一般情况约等于可用CPU数量）
```

##### 垃圾收集器之G1收集器
###### 与CMS收集器相比G1收集器有以下特点：

```
1. 能够像CMS收集器一样跟应用线程并行操作
2. 收缩空闲空间不会造成由长GC引起的应用停顿时间。
3. 更精确的预测GC停顿时间
4. 不会牺牲太多的吞吐效率
5. 不请求较多的Java堆
```

###### 区域划分

![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180116202027990-227900020.png)

###### 新生代收集
G1的新生代收集跟ParNew类似，当新生代占用达到一定比例的时候，开始出发收集。

![](http://dl2.iteye.com/upload/attachment/0113/8739/cadfa144-240d-3a02-9eee-c06a791b2eaf.png)	

被圈起的绿色部分为新生代的区域(region)，经过Young GC后存活的对象被复制到一个或者多个区域空闲中，这些被填充的区域将是新的新生代；当新生代对象的年龄(逃逸过一次Young GC年龄增加１)已经达到某个阈值(ParNew默认15)，被复制到老年代的区域中。

回收过程是停顿的(STW,Stop-The-Word);回收完成之后根据Young GC的统计信息调整Eden和Survivor的大小，有助于合理利用内存，提高回收效率。回收的过程多个回收线程并发收集。

---

### 内存分配与回收策略

[内存分配策略](https://www.cnblogs.com/shyroke/p/8299449.html)

#### 堆中优先分配Eden
大多数情况下，对象都在新生代的Eden区中分配内存。而因为大部分的对象都是“朝生夕死”的，所以新生代又会频繁进行垃圾回收。

```
public class Test02 {
    public static void main(String[] args) {
        byte [] byte01=new byte[2 * 1024 * 1024 ];
        byte [] byte02=new byte[2 * 1024 * 1024 ];
        byte [] byte03=new byte[2 * 1024 * 1024 ];
        byte [] byte04=new byte[4 * 1024 * 1024 ];
    
        System.gc();
    }
}
```

![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117000556006-540265788.png)	

结果
 
 ![111](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117000634678-1615632611.png)	
 
首先，-Xms、-Xmx分配用来设置进程堆内存的最小大小和最大大小。	
-Xmn用来设置堆内新生代的大小。通过这个值我们也可以得到老生代的大小：-Xmx减去-Xmn	
 -XX:SurvivorRatio=8 表示	
 
![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117001042896-1950461169.png)	

结果解析：
>正常来说，对象创建首先会分配到堆内存的eden区域，但是byte01~byte03对象共占6M内存，而byte04对象大小为4M，如果eden区域大小只有8M，故启动内存担保把byte01~byte03对象移到担保的内存中，也就是结果图中的
>![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117001447365-1806163811.png)


图中的内存大小就是6M，而此时byte04对象直接放到eden区域中。

#### 大对象直接进入老年代

```
需要大量连续空间的对象，
如：长字符串、数组等，会直接在老年代分配内存。
这是因为，这样可以避免在新生代区频繁的GC时发生大量的内存赋值（新生代的GC是采用复制算法的）。
```

#### 长期存活的对象进入老年代
新生代中经历了多次GC仍然存活的对象，当年龄达到一定程度（默认15）时就会晋升到老年代。


 为了更好地适应内存情况，虚拟机不是要求对象必须到达阀值才可晋升老年代的，
 而是采用动态年龄判定的方法：
 
 ```
如果Servivor空间中相同年龄的对象大小大于Servivor空间的一半时，
由于下一次的MinorGC时，这些对象如果仍然存活的话，复制到ToServivor空间时就放不下了。
所以，在本次GC时就可以把这些对象以及年龄大于等于这些对象的直接进入老年代。
```

在MinorGC时，如果Eden和FromServivor中存活的对象在复制到ToServivor时放不下了，也会直接分配到老年代。

#### 空间分配担保

```
在发生Minor GC之前，虚拟机会先检查老年代最大可用的连续空间是否大于新生代所有对象总空间，
如果这个条件成立，那么Minor GC可以确保是安全的。
如果不成立，则虚拟机会查看HandlePromotionFailure设置值是否允许担保失败。
如果允许，那么会继续检查老年代最大可用的连续空间是否大于历次晋升到老年代对象的平均大小，
如果大于，将尝试着进行一次Minor GC，尽管这次Minor GC是有风险的；
如果小于，或者HandlePromotionFailure设置不允许冒险，那这时也要改为进行一次Full GC。
```

---

### 栈上分配与逃逸分析

[栈上分配与逃逸分析](https://www.cnblogs.com/shyroke/p/8302934.html)

#### 什么是逃逸？

> 逃逸是指在某个方法之内创建的对象，除了在方法体之内被引用之外，还在方法体之外被其它变量引用到；这样带来的后果是在该方法执行完毕之后，该方法中创建的对象将无法被GC回收，由于其被其它变量引用。正常的方法调用中，方法体中创建的对象将在执行完毕之后，将回收其中创建的对象；故由于无法回收，即成为逃逸。

#### 栈上分配

> 分析找到未逃逸的变量，将变量类的实例化内存直接在栈里分配(无需进入堆)，分配完成后，继续在调用栈内执行，最后线程结束，栈空间被回收，局部变量对象也被回收。

#### 栈上分配与逃逸分析的关系

进行逃逸分析之后，产生的后果是所有的对象都将由栈上分配，而非从JVM内存模型中的堆来分配。

#### 逃逸分析／栈上分配的优劣分析
优势表现在以下两个方面：
* 消除同步

```
线程同步的代价是相当高的，同步的后果是降低并发性和性能。
逃逸分析可以判断出某个对象是否始终只被一个线程访问，
如果只被一个线程访问，那么对该对象的同步操作就可以转化成没有同步保护的操作，
这样就能大大提高并发程度和性能。
```

*  矢量替代

 ```
逃逸分析方法如果发现对象的内存存储结构不需要连续进行的话，就可以将对象的部分甚至全部都保存在CPU寄存器内，这样能大大提高访问速度。
```

劣势：  

```
栈上分配受限于栈的空间大小，一般自我迭代类的需求以及大的对象空间需求操作，将导致栈的内存溢出；故只适用于一定范围之内的内存范围请求。
 ```
 
 测试代码
 
```
package org.eds.homework.jvm;  
      
    public class StackOnTest {  
        public static void alloc() {  
            byte[] b = new byte[2];  
            b[0] = 1;  
        }  
      
        public static void main(String[] args) {  
            long b = System.currentTimeMillis();  
            for (int i = 0; i < 100000000; i++) {  
                alloc();  
            }  
            long e = System.currentTimeMillis();  
            System.out.println(e - b);  
        }  
      
    }
```
    
进行逃逸分析的设置

```   
设置jvm参数 ：-server -Xmx10m -Xms10m -XX:+DoEscapeAnalysis -XX:+PrintGC 
```

![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117145723631-1619064346.png)	

结果	

![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117145737724-607491324.png)

不进行逃逸分析的设置	

```
jvm参数设置：   -server -Xmx10m -Xms10m -XX:-DoEscapeAnalysis -XX:+PrintGC
```

结果	

![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117145831662-2057733453.png)

#### 总结
  在进行逃逸分析的运行结果中，只执行了9次就退出程序了。而未进行逃逸分析的结果是2081次，就是说未进行逃逸分析的代码可以执行更多的调用次数。换句话来讲，就是未进行逃逸分析的堆空间远大于进行逃逸分析后使用的栈空间，堆的空间大于栈，这就是根本原因。	
 栈上分配可以提升代码性能，降低在多线程情况下的锁使用，但是会受限于其空间的大小。

---

### java虚拟机性能监控工具

[ java虚拟机性能监控工具](https://www.cnblogs.com/shyroke/p/8305106.html)

####  jps(Java Virtual Machine Process Status Tool)  
jps主要用来输出JVM中运行的进程状态信息。语法格式如下：

![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117155606912-1036359482.png)

命令行参数选项说明如下：	

![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117155624724-701376587.png)

```
public class StackOnTest {  
    public static void alloc() {  
        byte[] b = new byte[2];  
        b[0] = 1;  
    }  
  
    public static void main(String[] args) {  
       Scanner sc=new Scanner(System.in);
       sc.nextLine();
    }  
  
}
```

上述代码执行三次，就有三个进程，注意此时不能在console里输入信息，这样程序就会卡在 Scanner sc=new Scanner(System.in); 这里，执行三次就有三个进程

![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117155947256-878754721.png)

#### jstack

 * jstack主要用来查看某个Java进程内的线程堆栈信息。语法格式如下：
 
![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117160122021-1223434428.png)
 
 * 命令行参数选项说明如下：
 
![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117160148959-1153278721.png)
 
#### jstat：类装载、内存、垃圾收集、JIT编译的信息
 
* 语法格式如下：
 
![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117161649021-2044620526.png)
 
* vmid是Java虚拟机ID，在Linux/Unix系统上一般就是进程ID。interval是采样时间间隔。count是采样数目。
 
结果
 
 ![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117164018537-326737841.png)
 
 解释结果图各列含义
 
 ![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117164039615-1021194204.png)
 
 #### jinfo：实时查看和调整虚拟机的各项参数
 
* jinfo使用介绍

可以用来查看正在运行的Java应用程序的扩展参数，甚至支持在运行时，修改部分参数

```
-flag <name> pid：打印指定JVM的参数值 
-flag [+|-]<name> pid：设置指定JVM参数的布尔值
-flag <name>=<value> pid：设置指定JVM参数的值
```
 
```
 public class JInfoTest {
    private static void s2() {
        String name = ManagementFactory.getRuntimeMXBean().getName();
        // get pid
        String pid = name.split("@")[0];
        System.out.println("Pid is:" + pid);

        while (true) {
            byte[] b = null;
            for (int i = 0; i < 10; i++)
                b = new byte[1 * 1024 * 1024];

            try {
                Thread.sleep(5000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public static void main(String[] args) {
        s2();
    }
}
```
 
* 设置jvm启动参数，如下：

![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117165938131-626106865.png)
 
* 运行程序，此时结果如下:
 
![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117170014115-1375799516.png)
 
* 使用jinfo，修改jvm参数
 
![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117170215131-914154505.png)
 
有一点必须注意：PrintGC必须开启，只开启PrintGCDetails、PrintGCTimeStamps不会输出GC，必须PrintGC同时开启

* 查看控制台，结果如下：
 
![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117170301568-489850263.png)
 
 #### jmap
 
*  jmap是输出内存中对象的工具，甚至可以将VM 中的heap以二进制输出成文本。可以监控JAVA程序是否有内存泄漏。
 
两种使用方法：
 
```
jmap -histo PID在内存使用峰值前后分别dump一次，可以对比出GC回收了哪些对象。

jmap -dump:format=b,file=f1 PID 将该内存heap输出到f1文件里，配合eclipse插件MemoryAnalyzer来使用
```
 
 ![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117174141740-93812621.png)
 
 ![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117174151412-1332197195.png)
 
 #### 使用Jconsole监控线程
 
 [Jconsole监控线程](https://www.cnblogs.com/shyroke/p/8305731.html)
 
 * 监控线程情况，包括阻塞、死循环等
 
```
package jvm;

import java.util.Scanner;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class JStackTest {
    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);
        sc.next();

        /**
         * 该线程死循环
         */
        new Thread(new Runnable() {

            @Override
            public void run() {
                while (true) {

                }

            }
        }, "mythread01").start();

        sc.next();

        /**
         * 该线程阻塞
         */
        testWait(new Object());

    }

    private static void testWait(Object obj) {
        new Thread(new Runnable() {

            @Override
            public void run() {

                synchronized (obj) {
                    try {
                        obj.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }

            }
        }, "mythread02").start();

    }
}
```
 
* 运行程序，打开jconsole，结果如下图，此时只有main线程，且该线程状态为Runable，main线程阻塞因为正在等待用户输入（sc.next()；）。
 
![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117201023990-1065766871.png)
 
* 在console输入一次之后，如下图
 
![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117201309162-437429724.png)
 
* 再查看jconsole，结果如下，发现多了一个mythread01线程，且该线程也为Runable状态，查看代码发现该线程是死循环所以阻塞。

![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117201402115-1912822456.png)
 
* 再在console输入一次，如下图
 
![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117201531896-559177314.png)

* 再查看jconsole，结果如下，main线程已经执行完毕了，所以消失了。mythread01线程还在执行（死循环），多了mythread02线程，该线程为WATING状态

![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180117201618365-1578860542.png)

#### 使用 VisualVM 进行性能分析及调优
[使用 VisualVM 进行性能分析及调优](https://www.ibm.com/developerworks/cn/java/j-lo-visualvm/index.html)

---

### 调优案例分析与实战

#### JVM 调优 —— GC 长时间停顿问题及解决方法

[GC 长时间停顿问题及解决方法](http://www.voidcn.com/article/p-hedvirvq-bac.html)

*  简介
垃圾收集器长时间停顿，表现在 Web 页面上可能是页面响应码 500 之类的服务器错误问题，如果是个支付过程可能会导致支付失败，将造成公司的直接经济损失，程序员要尽量避免或者说减少此类情况发生。

##### 一.并发模式失败

* 并发模式失败日志

```
2016-02-21T13:53:07.974+0800: 171467.254: [GC [1 CMS-initial-mark: 1436199K(1560576K)] 1512927K(2504320K), 0.0618140 secs] [Times: user=0.06 sys=0.00, real=0.07 secs]
2016-02-21T13:53:08.036+0800: 171467.316: [CMS-concurrent-mark-start]
2016-02-21T13:53:08.377+0800: 171467.657: [CMS-concurrent-mark: 0.340/0.340 secs] [Times: user=2.11 sys=0.15, real=0.34 secs]
2016-02-21T13:53:08.377+0800: 171467.657: [CMS-concurrent-preclean-start]
2016-02-21T13:53:08.385+0800: 171467.665: [CMS-concurrent-preclean: 0.008/0.008 secs] [Times: user=0.01 sys=0.00, real=0.01 secs]
2016-02-21T13:53:08.385+0800: 171467.665: [CMS-concurrent-abortable-preclean-start]
{Heap before GC invocations=88667 (full 45):
 par new generation   total 943744K, used 914399K [0x0000000757000000, 0x0000000797000000, 0x0000000797000000)
  eden space 838912K, 100% used [0x0000000757000000, 0x000000078a340000, 0x000000078a340000)
  from space 104832K,  72% used [0x000000078a340000, 0x000000078ecf7d98, 0x00000007909a0000)
  to   space 104832K,   0% used [0x00000007909a0000, 0x00000007909a0000, 0x0000000797000000)
 concurrent mark-sweep generation total 1560576K, used 1436199K [0x0000000797000000, 0x00000007f6400000, 0x00000007f6400000)
 concurrent-mark-sweep perm gen total 159744K, used 78413K [0x00000007f6400000, 0x0000000800000000, 0x0000000800000000)
2016-02-21T13:53:09.535+0800: 171468.815: [GC2016-02-21T13:53:09.535+0800: 171468.815: [ParNew
Desired survivor size 53673984 bytes, new threshold 6 (max 6)
- age   1:    7100568 bytes,    7100568 total
- age   2:    4676456 bytes,   11777024 total
- age   3:    8773736 bytes,   20550760 total
- age   4:    7709744 bytes,   28260504 total
- age   5:   10891960 bytes,   39152464 total
- age   6:   11735032 bytes,   50887496 total
: 914399K->75616K(943744K), 0.0414860 secs] 2350599K->1517225K(2504320K), 0.0417710 secs] [Times: user=0.32 sys=0.05, real=0.05 secs]
Heap after GC invocations=88668 (full 45):
 par new generation   total 943744K, used 75616K [0x0000000757000000, 0x0000000797000000, 0x0000000797000000)
  eden space 838912K,   0% used [0x0000000757000000, 0x0000000757000000, 0x000000078a340000)
  from space 104832K,  72% used [0x00000007909a0000, 0x0000000795378128, 0x0000000797000000)
  to   space 104832K,   0% used [0x000000078a340000, 0x000000078a340000, 0x00000007909a0000)
 concurrent mark-sweep generation total 1560576K, used 1441609K [0x0000000797000000, 0x00000007f6400000, 0x00000007f6400000)
 concurrent-mark-sweep perm gen total 159744K, used 78413K [0x00000007f6400000, 0x0000000800000000, 0x0000000800000000)
}
2016-02-21T13:53:10.202+0800: 171469.482: [CMS-concurrent-abortable-preclean: 1.772/1.817 secs] [Times: user=4.94 sys=0.06, real=1.81 secs]
2016-02-21T13:53:10.204+0800: 171469.483: [GC[YG occupancy: 497152 K (943744 K)]2016-02-21T13:53:10.204+0800: 171469.483: [Rescan (parallel) , 1.3691900 secs]2016-02-21T13:53:11.573+0800: 171470.853: [weak refs processing, 0.1009300 secs]2016-02-21T13:53:11.674+0800: 1714
70.954: [class unloading, 0.0153470 secs]2016-02-21T13:53:11.689+0800: 171470.969: [scrub symbol table, 0.0110770 secs]2016-02-21T13:53:11.700+0800: 171470.980: [scrub string table, 0.0016360 secs] [1 CMS-remark: 1441609K(1560576K)] 1938761K(2504320K), 1.5079530 secs] [Ti
mes: user=13.01 sys=0.08, real=1.51 secs]
2016-02-21T13:53:11.712+0800: 171470.992: [CMS-concurrent-sweep-start]
{Heap before GC invocations=88668 (full 45):
 par new generation   total 943744K, used 914528K [0x0000000757000000, 0x0000000797000000, 0x0000000797000000)
  eden space 838912K, 100% used [0x0000000757000000, 0x000000078a340000, 0x000000078a340000)
  from space 104832K,  72% used [0x00000007909a0000, 0x0000000795378128, 0x0000000797000000)
  to   space 104832K,   0% used [0x000000078a340000, 0x000000078a340000, 0x00000007909a0000)
 concurrent mark-sweep generation total 1560576K, used 1441606K [0x0000000797000000, 0x00000007f6400000, 0x00000007f6400000)
 concurrent-mark-sweep perm gen total 159744K, used 78413K [0x00000007f6400000, 0x0000000800000000, 0x0000000800000000)
2016-02-21T13:53:11.775+0800: 171471.055: [GC2016-02-21T13:53:11.775+0800: 171471.055: [ParNew (promotion failed)
Desired survivor size 53673984 bytes, new threshold 6 (max 6)
- age   1:    4030872 bytes,    4030872 total
- age   2:    5959704 bytes,    9990576 total
- age   3:    4628680 bytes,   14619256 total
- age   4:    8773080 bytes,   23392336 total
- age   5:    7707144 bytes,   31099480 total
- age   6:   10890224 bytes,   41989704 total
: 914528K->907344K(943744K), 1.0312010 secs]2016-02-21T13:53:12.807+0800: 171472.086: [CMS2016-02-21T13:53:14.455+0800: 171473.735: [CMS-concurrent-sweep: 1.684/2.743 secs] [Times: user=3.69 sys=0.36, real=2.74 secs]
 (concurrent mode failure): 1451903K->475795K(1560576K), 3.9644230 secs] 2356134K->475795K(2504320K), [CMS Perm : 78413K->78413K(159744K)], 4.9959570 secs] [Times: user=5.65 sys=0.34, real=5.00 secs]
Heap after GC invocations=88669 (full 46):
 par new generation   total 943744K, used 0K [0x0000000757000000, 0x0000000797000000, 0x0000000797000000)
  eden space 838912K,   0% used [0x0000000757000000, 0x0000000757000000, 0x000000078a340000)
  from space 104832K,   0% used [0x000000078a340000, 0x000000078a340000, 0x00000007909a0000)
  to   space 104832K,   0% used [0x00000007909a0000, 0x00000007909a0000, 0x0000000797000000)
 concurrent mark-sweep generation total 1560576K, used 475795K [0x0000000797000000, 0x00000007f6400000, 0x00000007f6400000)
 concurrent-mark-sweep perm gen total 159744K, used 78413K [0x00000007f6400000, 0x0000000800000000, 0x0000000800000000)
}

```


两个原因：

```
在 CMS 启动过程中，新生代提升速度过快，老年代收集速度赶不上新生代提升速度
在 CMS 启动过程中，老年代碎片化严重，无法容纳新生代提升上来的大对象
```

发送这种情况，应用线程将会全部停止（相当于网站这段时间无法响应用户请求），进行压缩式垃圾收集（回退到 Serial Old 算法）

解决办法：

新生代提升过快问题：

```
（1）如果频率太快的话，说明空间不足，首先可以尝试调大新生代空间和晋升阈值。
（2）如果内存有限，可以设置 CMS 垃圾收集在老年代占比达到多少时启动来减少问题发生频率（越早启动问题发生频率越低，但是会降低吞吐量，具体得多调整几次找到平衡点），
```

参数如下：

```
如果没有第二个参数，会随着 JVM 动态调节 CMS 启动时间
-XX:CMSInitiatingOccupancyFraction=68 （默认是 68）
-XX:+UseCMSInitiatingOccupancyOnly
```

老年代碎片严重问题：

```
（1）如果频率太快或者 Full GC 后空间释放不多的话，说明空间不足，首先可以尝试调大老年代空间
（2）如果内存不足，可以设置进行 n 次 CMS 后进行一次压缩式 Full GC，
```

参数如下：

```
-XX:+UseCMSCompactAtFullCollection：允许在 Full GC 时，启用压缩式 GC	
-XX:CMSFullGCBeforeCompaction=n     在进行 n 次，CMS 后，进行一次压缩的 Full GC，用以减少 CMS 产生的碎片
```

##### 二.提升失败（promotion failed）

在 Minor GC 过程中， Survivor Unused 可能不足以容纳 Eden 和另一个 Survivor 中的存活对象， 那么多余的将被移到老年代， 称为 过早提升（Premature Promotion）。 这会导致老年代中短期存活对象的增长， 可能会引发严重的性能问题。  再进一步， 如果老年代满了， Minor GC 后会进行 Full GC， 这将导致遍历整个堆， 称为 提升失败（Promotion Failure）。

* 提升失败日志

```
2016-01-07T18:54:26.948+0800: 18782.967: [GC2016-04-07T18:54:26.948+0800: 18782.967: [ParNew (promotion failed)
Desired survivor size 117833728 bytes, new threshold 10 (max 10)
- age   1:    6141680 bytes,    6141680 total
- age   2:    6337936 bytes,   12479616 total
- age   3:     549120 bytes,   13028736 total
- age   4:      87768 bytes,   13116504 total
- age   5:     221384 bytes,   13337888 total
- age   6:     934168 bytes,   14272056 total
- age   7:     146072 bytes,   14418128 total
- age   8:     626064 bytes,   15044192 total
- age   9:     398000 bytes,   15442192 total
- age  10:     429616 bytes,   15871808 total
: 1969227K->1929200K(2071808K), 0.7452140 secs]2016-01-07T18:54:27.693+0800: 18783.713: [CMS: 1394703K->632845K(2097152K), 4.0993640 secs] 3301676K->632845K(4168960K), [CMS Perm : 77485K->77473K(159744K)], 4.8450240 secs] [Times: user=5.18 sys=0.56, real=4.84 secs]
Heap after GC invocations=5847 (full 7):
 par new generation   total 2071808K, used 0K [0x00000006e9c00000, 0x0000000776400000, 0x0000000776400000)
  eden space 1841664K,   0% used [0x00000006e9c00000, 0x00000006e9c00000, 0x000000075a280000)
  from space 230144K,   0% used [0x0000000768340000, 0x0000000768340000, 0x0000000776400000)
  to   space 230144K,   0% used [0x000000075a280000, 0x000000075a280000, 0x0000000768340000)
 concurrent mark-sweep generation total 2097152K, used 632845K [0x0000000776400000, 0x00000007f6400000, 0x00000007f6400000)
 concurrent-mark-sweep perm gen total 159744K, used 77473K [0x00000007f6400000, 0x0000000800000000, 0x0000000800000000)
}

```

提升失败原因：
Minor GC 时发现 Survivor 空间放不下，而老年代的空闲也不够

```
新生代提升太快
老年代碎片太多，放不下大对象提升（表现为老年代还有很多空间但是，出现了 promotion failed）
```

解决方法：

```
两条和上面 concurrent mode failure 一样
另一条，是因为 Survivor Unused 不足，那么可以尝试调大 Survivor 来尝试下 
```

#### 堆外内存导致的溢出错误

[堆外内存导致的溢出错误](https://my.oschina.net/xiaowangqiongyou/blog/1790267)

[从0到1起步-跟我进入堆外内存的奇妙世界](https://www.jianshu.com/p/50be08b54bee)

* 堆内内存（on-heap memory）回顾

堆外内存和堆内内存是相对的二个概念，其中堆内内存是我们平常工作中接触比较多的，我们在jvm参数中只要使用-Xms，-Xmx等参数就可以设置堆的大小和最大值，理解jvm的堆还需要知道下面这个公式：

> 堆内内存 = 新生代+老年代+持久代

如下面的图所示：

![](https://upload-images.jianshu.io/upload_images/1049928-b799abaaa261293d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/634/format/webp)

在使用堆内内存（on-heap memory）的时候，完全遵守JVM虚拟机的内存管理机制，采用垃圾回收器（GC）统一进行内存管理，GC会在某些特定的时间点进行一次彻底回收，也就是Full GC，GC会对所有分配的堆内内存进行扫描，在这个过程中会对JAVA应用程序的性能造成一定影响，还可能会产生Stop The World。

* 堆外内存（off-heap memory）介绍

和堆内内存相对应，堆外内存就是把内存对象分配在Java虚拟机的堆以外的内存，这些内存直接受操作系统管理（而不是虚拟机），这样做的结果就是能够在一定程度上减少垃圾回收对应用程序造成的影响。	
作为JAVA开发者我们经常用java.nio.DirectByteBuffer对象进行堆外内存的管理和使用，它会在对象创建的时候就分配堆外内存。	
DirectByteBuffer类是在Java Heap外分配内存，对堆外内存的申请主要是通过成员变量unsafe来操作，下面介绍构造方法

```
DirectByteBuffer(int cap) {                 

        super(-1, 0, cap, cap);
        //内存是否按页分配对齐
        boolean pa = VM.isDirectMemoryPageAligned();
        //获取每页内存大小
        int ps = Bits.pageSize();
        //分配内存的大小，如果是按页对齐方式，需要再加一页内存的容量
        long size = Math.max(1L, (long)cap + (pa ? ps : 0));
        //用Bits类保存总分配内存(按页分配)的大小和实际内存的大小
        Bits.reserveMemory(size, cap);

        long base = 0;
        try {
           //在堆外内存的基地址，指定内存大小
            base = unsafe.allocateMemory(size);
        } catch (OutOfMemoryError x) {
            Bits.unreserveMemory(size, cap);
            throw x;
        }
        unsafe.setMemory(base, size, (byte) 0);
        //计算堆外内存的基地址
        if (pa && (base % ps != 0)) {
            // Round up to page boundary
            address = base + ps - (base & (ps - 1));
        } else {
            address = base;
        }
        cleaner = Cleaner.create(this, new Deallocator(base, size, cap));
        att = null;
    }
```

注：在Cleaner 内部中通过一个列表，维护了一个针对每一个 directBuffer 的一个回收堆外内存的 线程对象(Runnable)，回收操作是发生在 Cleaner 的 clean() 方法中。

```
private static class Deallocator implements Runnable  {
    private static Unsafe unsafe = Unsafe.getUnsafe();
    private long address;
    private long size;
    private int capacity;
    private Deallocator(long address, long size, int capacity) {
        assert (address != 0);
        this.address = address;
        this.size = size;
        this.capacity = capacity;
    }
 
    public void run() {
        if (address == 0) {
            // Paranoia
            return;
        }
        unsafe.freeMemory(address);
        address = 0;
        Bits.unreserveMemory(size, capacity);
    }
}
```

* 使用堆外内存的优点

1、减少了垃圾回收

```
因为垃圾回收会暂停其他的工作。
```

2、加快了复制的速度

```
堆内在flush到远程时，会先复制到直接内存（非堆内存），然后在发送；而堆外内存相当于省略掉了这个工作。
同样任何一个事物使用起来有优点就会有缺点，
堆外内存的缺点就是内存难以控制，使用了堆外内存就间接失去了JVM管理内存的可行性，改由自己来管理，当发生内存溢出时排查起来非常困难。
```

* 使用DirectByteBuffer的注意事项

java.nio.DirectByteBuffer对象在创建过程中会先通过Unsafe接口直接通过os::malloc来分配内存，然后将内存的起始地址和大小存到java.nio.DirectByteBuffer对象里，这样就可以直接操作这些内存。这些内存只有在DirectByteBuffer回收掉之后才有机会被回收，因此如果这些对象大部分都移到了old，但是一直没有触发CMS GC或者Full GC，那么悲剧将会发生，因为你的物理内存被他们耗尽了，因此为了避免这种悲剧的发生，通过-XX:MaxDirectMemorySize来指定最大的堆外内存大小，当使用达到了阈值的时候将调用System.gc来做一次full gc，以此来回收掉没有被使用的堆外内存。


* 现象

```
发现服务器不定时地会抛出内存溢出异常；
尝试把堆开到最大（32位系统最多到1.6G），没有任何效果，反而异常频繁；
加入-XX:+HeapDumpOnOutOfMemoryError，没有任何反应，内存溢出时不会产生dump文件；
jstat工具监测，GC并不频繁；
```

查看系统日志如下：

```
[org.eclipse.jetty.util.log] handle failed java.lang.OutOfMemoryError: null 
at sun.misc.Unsafe.allocateMemory(Natave Method)
at java.nio.DirecByteBuffer.<init>(DirectByteBuffer.java:99)
at java.nio.ByteBuffer.allocateDirect(ByteBuffer.java:288)\
at org.eclipse.jetty.io.nio.DirectNIOBuffer.<init>
......
```

* 分析

此时，看到上面的异常现象后，可以得知异常的原因是因为堆外内存不够引起。	
32位系统下，给Java堆分配的内存越大，堆外内存越小，因为总量是固定的，最大是2G。

异常关键原因：

```
垃圾收集时，虚拟机会对Direct Memory进行回收，但是不会主动回收。
只能等到老年代满了后，发送Full GC时，才会对Direct Memory进行回收，这是一个“顺便清理”的过程。所以，当Direct Memory不够用时，自然会抛出内存溢出异常。
```

#### 服务器JVM进程崩溃

[JVM笔记 – 自动内存管理机制（调优案例分析与实战）](http://www.itzhai.com/jvm-note-automatic-memory-management-mechanism-4.html)

跨系统集成的时候，使用到异步方式调用Web服务，由于两边服务速度不读等，导致很多Web服务没有调用完成，在等待的线程和Socket连接越来越多，超过JVM的承受范围后JVM进程就崩溃了。

> 可以将异步调用改为生产者/消费者模式的消息队列实现。(加缓冲)

---

### class 文件结构

#### Java体系结构

![](https://images2017.cnblogs.com/blog/1072930/201801/1072930-20180120223301631-1490807525.png)

#### class格式文件概述

* class文件是一种8位字节的二进制流文件， 各个数据项按顺序紧密的从前向后排列， 相邻的项之间没有间隙， 这样可以使得class文件非常紧凑， 体积轻巧， 可以被JVM快速的加载至内存， 并且占据较少的内存空间。 我们的Java源文件， 在被编译之后， 每个类（或者接口）都单独占据一个class文件， 并且类中的所有信息都会在class文件中有相应的描述， 由于class文件很灵活， 它甚至比Java源文件有着更强的描述能力。

* class文件中的信息是一项一项排列的， 每项数据都有它的固定长度， 有的占一个字节， 有的占两个字节， 还有的占四个字节或8个字节， 数据项的不同长度分别用u1, u2, u4, u8表示， 分别表示一种数据项在class文件中占据一个字节， 两个字节， 4个字节和8个字节。 可以把u1, u2, u3, u4看做class文件数据项的“类型” 。

#### Class文件中存储数据的类型：无符号数和表

[深入理解JVM之Java字节码（.class）文件详解](https://windysha.github.io/2018/01/18/%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3JVM%E4%B9%8BJava%E5%AD%97%E8%8A%82%E7%A0%81%EF%BC%88-class%EF%BC%89%E6%96%87%E4%BB%B6%E8%AF%A6%E8%A7%A3/)

* 无符号数（基本数据类型）：

```
以u1、u2、u4、u8来分别代表1个字节、2个字节、4个字节和8个字节的无符号数，
无符号数可以用来描述数字、索引引用、数量值或者按照UTF-8编码构成字符串值。
```

* 表（复合数据类型）：

```
是由多个无符号数或者其他表作为数据项构成的复合数据类型，
所有表都习惯性地“_info”结尾。
表用于描述有层次关系的复合结构的数据。
表是一个统称，就好比把ArrayList、LinkedList、Set都是称为集合(Collection)，但是每个集合的内部结构都是不同的，
Class中有很多不同的表。
如下图中cp_info类型,是表类型，但是它是一个固定结构的类型吗？
不是，它好比Collection集合下的List集合，只是一类集合的统称，实际上cp_info表是14种具体表类型的统称，constant_pool_count-1指出了有多少个cp_info表，
那到底是哪些具体的表，就需要具体看了。
```

一个典型的class文件分为：MagicNumber，Version，Constant_pool，Access_flag，This_class，Super_class，Interfaces，Fields，Methods 和Attributes这十个部分，用一个数据结构可以表示如下：

![](http://static.zybuluo.com/Wind729/firt103iv1wox6jb2mrkpt4r/class_code.PNG)

下面对class文件中的每一项进行详细的解释：

1、magic

```
在class文件开头的四个字节， 存放着class文件的魔数， 
这个魔数是class文件的标志，他是一个固定的值： 0XCAFEBABE 。 
也就是说他是判断一个文件是不是class格式的文件的标准， 
如果开头四个字节不是0XCAFEBABE， 那么就说明它不是class文件， 不能被JVM识别。
```

2、minor_version 和 major_version

紧接着魔数的四个字节是class文件的此版本号和主版本号。

```
随着Java的发展， class文件的格式也会做相应的变动。 
版本号标志着class文件在什么时候， 加入或改变了哪些特性。 
举例来说， 不同版本的javac编译器编译的class文件， 版本号可能不同， 而不同版本的JVM能识别的class文件的版本号也可能不同，
一般情况下， 高版本的JVM能识别低版本的javac编译器编译的class文件， 
而低版本的JVM不能识别高版本的javac编译器编译的class文件。 
如果使用低版本的JVM执行高版本的class文件， JVM会抛出java.lang.UnsupportedClassVersionError 。
具体的版本号变迁这里不再讨论， 需要的读者自行查阅资料。
```

3、constant_pool

在class文件中， 位于版本号后面的就是常量池相关的数据项。 

```
常量池是class文件中的一项非常重要的数据。 
常量池中存放了文字字符串， 常量值， 当前类的类名， 字段名， 方法名， 各个字段和方法的描述符， 对当前类的字段和方法的引用信息， 当前类中对其他类的引用信息等等。 
常量池中几乎包含类中的所有信息的描述，
class文件中的很多其他部分都是对常量池中的数据项的引用，
比如后面要讲到的this_class, super_class, field_info, attribute_info等， 
另外字节码指令中也存在对常量池的引用， 这个对常量池的引用当做字节码指令的一个操作数。
此外，常量池中各个项也会相互引用。
```

class文件中的项constant_pool_count的值为1, 说明每个类都只有一个常量池。 常量池中的数据也是一项一项的， 没有间隙的依次排放。常量池中各个数据项通过索引来访问， 有点类似与数组， 只不过常量池中的第一项的索引为1, 而不为0, 如果class文件中的其他地方引用了索引为0的常量池项， 就说明它不引用任何常量池项。
class文件中的每一种数据项都有自己的类型， 相同的道理，常量池中的每一种数据项也有自己的类型。 
常量池中的数据项的类型如下表：

![](http://static.zybuluo.com/Wind729/y0rw90a07nqpi1koeisr29bb/constant_pool.PNG)

每个数据项叫做一个XXX_info项，比如，一个常量池中一个CONSTANT_Utf8类型的项，就是一个CONSTANT_Utf8_info 。除此之外， 每个info项中都有一个标志值（tag），这个标志值表明了这个常量池中的info项的类型是什么， 从上面的表格中可以看出，一个CONSTANT_Utf8_info中的tag值为1，而一个CONSTANT_Fieldref_info中的tag值为9 。

4、access_flag 保存了当前类的访问权限

5、this_cass 保存了当前类的全局限定名在常量池里的索引

6、super class 保存了当前类的父类的全局限定名在常量池里的索引

7、interfaces 保存了当前类实现的接口列表，包含两部分内容：

```
interfaces_count 和interfaces[interfaces_count]
interfaces_count 指的是当前类实现的接口数目
interfaces[] 是包含interfaces_count个接口的全局限定名的索引的数组
```

8、fields 保存了当前类的成员列表，包含两部分的内容：

```
fields_count 和 fields[fields_count]
fields_count是类变量和实例变量的字段的数量总和。
fileds[]是包含字段详细信息的列表。
```

9、methods 保存了当前类的方法列表，包含两部分的内容：

```
methods_count和methods[methods_count]
methods_count是该类或者接口显示定义的方法的数量。
method[]是包含方法信息的一个详细列表。
```

10、attributes 包含了当前类的attributes列表，包含两部分内容：

```
attributes_count 和 attributes[attributes_count]
```

>class文件的最后一部分是属性，它描述了该类或者接口所定义的一些属性信息。attributes_count指的是attributes列表中包含的attribute_info的数量。
属性可以出现在class文件的很多地方，而不只是出现在attributes列表里。如果是attributes表里的属性，那么它就是对整个class文件所对应的类或者接口的描述；如果出现在fileds的某一项里，那么它就是对该字段额外信息的描述；如果出现在methods的某一项里，那么它就是对该方法额外信息的描述。

#### 通过示例代码来手动分析class文件

我们在这里新建一个java文件，Hello.java，具体内容如下：

```
public class Hello{
  private int test;
  public int test(){
        return test;
    }
}
```

然后再通过javac命令将此java文件编译成class文件：

```
javac /d/class_file_test/Hello.java
```

编译之后的class文件十六进制结果如下所示，可以用UltraEdit等十六进制编辑器打开，得到：

![](http://static.zybuluo.com/Wind729/xxy7sc8k65wd060vl7h47hnh/hello_class_test.PNG)

接下来我们就按照class文件的格式来分析上面的一串数字，还是按照之前的顺序来：
* magic:

CA FE BA BE ，代表该文件是一个字节码文件，我们平时区分文件类型都是通过后缀名来区分的，不过后缀名是可以随便修改的，所以仅靠后缀名不能真正区分一个文件的类型。区分文件类型的另个办法就是magic数字，JVM 就是通过 CA FE BA BE 来判断该文件是不是class文件

* version字段：

00 00 00 34，前两个字节00是minor_version，后两个字节0034是major_version字段，对应的十进制值为52，也就是说当前class文件的主版本号为52，次版本号为0。下表是jdk 1.6 以后对应支持的 Class 文件版本号：

![](http://static.zybuluo.com/Wind729/pwtmpc9fws585t7h2dtdb0ml/image_1c2th3nii1otd13vg1vhg1hl6itg4i.png)

* 常量池，constant_pool:

3.1. constant_pool_count

紧接着version字段下来的两个字节是：00 12代表常量池里包含的常量数目，因为字节码的常量池是从1开始计数的，这个常量池包含17个（0x0012-1）常量。


3.2.constant_pool

接下来就是分析这17个常量:

3.2.1. 第一个变量 0A 00 04 00 0E	
首先，紧接着constant_pool_count的第一个字节0a（tag=10）

![](http://static.zybuluo.com/Wind729/0lt2xeea9935vhps75yokr9d/image_1c2tj6ib6pslkbb1876ot81rjj4v.png)

可知，这表示的是一个CONSTANT_Methodref。CONSTANT_Methodref的结构如下：

```
CONSTANT_Methodref_info {
          u1 tag;    //u1表示占一个字节
          u2 class_index;    //u2表示占两个字节
          u2 name_and_type_index;    //u2表示占两个字节
}
```

其中class_index表示该方法所属的类在常量池里的索引，name_and_type_index表示该方法的名称和类型的索引。	
常量池里的变量的索引从1开始。

那么这个methodref结构的数据如下：

```
0a  //tag  10表示这是一个CONSTANT_Methodref_info结构
00 04 //class_index 指向常量池中第4个常量所表示的类
00 0e  //name_and_type_index 指向常量池中第14个常量所表示的方法
```

3.2.2. 第二个变量09 00 03 00 0F		
接着是第二个常量，它的tag是09，根据上面的表格可知，这表示的是一个CONSTANT_Fieldref的结构，它的结构如下：

```
CONSTANT_Fieldref_info {
      u1 tag;
      u2 class_index;
      u2 name_and_type_index;
}
```

和上面的变量基本一致。

```
09 //tag
00 03 //指向常量池中第3个常量所表示的类
00 0f //指向常量池中第15个常量所表示的变量
```

3.2.3. 第三个变量 07 00 10	

tag为07表示是一个CONSTANT_Class变量，这个变量的结构如下：

```
CONSTANT_Class_info {
          u1 tag;
          u2 name_index;
}
```

除了tag字段以外，还有一个name_index的值为00 10，即是指向常量池中第16个常量所表示的Class名称。

3.2.4. 第四个变量07 00 11	
同上，也是一个CONSTANT_Class变量，不过，指向的是第17个常量所表示的Class名称。

3.2.5. 第五个变量 01 00 04 74 65 73 74	
tag为1，表示这是一个CONSTANT_Utf8结构，这种结构用UTF-8的一种变体来表示字符串，结构如下所示：

```
CONSTANT_Utf8_info {
               u1 tag;
               u2 length;
               u1 bytes[length];
}
```

其中length表示该字符串的字节数，bytes字段包含该字符串的二进制表示。

```
01 //tag  1表示这是一个CONSTANT_Utf8结构
00 04 //表示这个字符串的长度是4字节,也就是后面的四个字节74 65 73 74
74 65 73 74 //通过ASCII码表转换后，表示的是字符串“test”
```

接下来的8个变量都是字符串，这里就不具体分析了。

3.2.6. 第十四个常量 0c 00 07 00 08	
tag为0c，表示这是一个CONSTANT_NameAndType结构，这个结构用来描述一个方法或者成员变量。具体结构如下：

```
CONSTANT_NameAndType_info {
          u1 tag;
          u2 name_index;
          u2 descriptor_index;
}
```

name_index表示的是该变量或者方法的名称，这里的值是0007，表示指向第7个常量，即是<init>。

descriptor_index指向该方法的描述符的引用，这里的值是0008，表示指向第8个常量，即是()V，由前面描述符的语法可知，这个方法是一个无参的，返回值为void的方法。

综合两个字段，可以推出这个方法是void <init>()。也即是指向这个NameAndType结构的Methodref的方法名为void <init>()，也就是说第一个常量表示的是void <init>()方法，这个方法其实就是此类的默认构造方法。

3.2.7. 第十五个常量也是一个CONSTANT_NameAndType，表示的方法名为“int test()”，第2个常量引用了这个NameAndType，所以第二个常量表示的是“int test()”方法。

3.2.8. 第16和17个常量也是字符串，可以按照前面的方法分析。

3.3. 完整的常量池

最后，通过以上分析，完整的常量池如下：	
	
```
00 12  常量池的数目 18-1=17
0a 00 04 00 0e  方法：java.lang.Ojbect void <init>()
09 00 03 00 0f   方法 ：Hello int test() 
07 00 10  字符串：Hello
07 00 11 字符串：java.lang.Ojbect
01 00 04 74 65 73 74 字符串：test
01 00 01 49  字符串：I
01 00 06 3c 69 6e 69 74 3e 字符串：<init>
01 00 03 28 29 56 字符串：()V
01 00 04 43 6f 64 65 字符串：Code 
01 00 0f 4c 69 6e 65 4e 75 6d 62 65 72 54 61 62 6c 65 字符串：LineNumberTable 
01 00 03 28 29 49 字符串：()I
01 00 0a 53 6f 75 72 63 65 46 69 6c 65 字符串：SourceFile
01 00 0a 48 65 6c 6c 6f 2e 6a 61 76 61 字符串：Hello.java
0c 00 07 00 08 NameAndType：<init> ()V
0c 00 05 00 06 NameAndType：test I
01 00 05 48 65 6c 6c 6f 字符串：Hello
01 00 10 6a 61 76 61 2f 6c 61 6e 67 2f 4f 62 6a 65 63 74 字符串： java/lang/Object
```

通过这样分析其实非常的累，我们只是为了了解class文件的原理才来一步一步分析每一个二进制字节码。JDK提供了现成的工具可以直接解析此二进制文件，即javap工具(在JDK的bin目录下)，我们通过javap命令来解析此class文件：

```
javap -v -p -s -sysinfo -constants /d/class_file_test/Hello.class
```

解析得到的结果为：

![](http://static.zybuluo.com/Wind729/y3px0bt1fj1qclzo80apjhbb/class_file_2.PNG)

---

### java类的加载机制

[java类的加载机制](https://mp.weixin.qq.com/s?__biz=MzI4NDY5Mjc1Mg==&mid=2247483934&idx=1&sn=41c46eceb2add54b7cde9eeb01412a90&chksm=ebf6da61dc81537721d36aadb5d20613b0449762842f9128753e716ce5fefe2b659d8654c4e8&scene=21#wechat_redirect)

#### 什么是类的加载

类的加载指的是将类的.class文件中的二进制数据读入到内存中，将其放在运行时数据区的方法区内，然后在堆区创建一个 java.lang.Class对象，用来封装类在方法区内的数据结构。类的加载的最终产品是位于堆区中的 Class对象， Class对象封装了类在方法区内的数据结构，并且向Java程序员提供了访问方法区内的数据结构的接口。

![](https://mmbiz.qpic.cn/mmbiz_png/PgqYrEEtEnokxXiapDdvntH8PGwa0zGXMqaq9p1LDCF7iadMBibd685uYCy8u0yhb5dmlvLRwgzNueNdSdWdvEwww/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

类加载器并不需要等到某个类被“首次主动使用”时再加载它，JVM规范允许类加载器在预料某个类将要被使用时就预先加载它，如果在预先加载的过程中遇到了.class文件缺失或存在错误，类加载器必须在程序首次主动使用该类时才报告错误（LinkageError错误）如果这个类一直没有被程序主动使用，那么类加载器就不会报告错误

#### 加载.class文件的方式

* 从本地系统中直接加载

* 通过网络下载.class文件

* 从zip，jar等归档文件中加载.class文件

* 从专有数据库中提取.class文件

* 将Java源文件动态编译为.class文件

#### 类的生命周期

![](https://mmbiz.qpic.cn/mmbiz_png/PgqYrEEtEnokxXiapDdvntH8PGwa0zGXMyIBnM38m8eKia8wAVY8aXb0NhM9wFNDLVuoFKIZ0Q2SBk5yibFgXsXOw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

其中类加载的过程包括了加载、验证、准备、解析、初始化五个阶段。在这五个阶段中，加载、验证、准备和初始化这四个阶段发生的顺序是确定的，而解析阶段则不一定，它在某些情况下可以在初始化阶段之后开始，这是为了支持Java语言的运行时绑定（也成为动态绑定或晚期绑定）。另外注意这里的几个阶段是按顺序开始，而不是按顺序进行或完成，因为这些阶段通常都是互相交叉地混合进行的，通常在一个阶段执行的过程中调用或激活另一个阶段。

#### 加载
查找并加载类的二进制数据加载时类加载过程的第一个阶段，在加载阶段，虚拟机需要完成以下三件事情：

* 通过一个类的全限定名来获取其定义的二进制字节流。

* 将这个字节流所代表的静态存储结构转化为方法区的运行时数据结构。

* 在Java堆中生成一个代表这个类的 java.lang.Class对象，作为对方法区中这些数据的访问入口。

相对于类加载的其他阶段而言，加载阶段（准确地说，是加载阶段获取类的二进制字节流的动作）是可控性最强的阶段，因为开发人员既可以使用系统提供的类加载器来完成加载，也可以自定义自己的类加载器来完成加载。

加载阶段完成后，虚拟机外部的二进制字节流就按照虚拟机所需的格式存储在方法区之中，而且在Java堆中也创建一个 java.lang.Class类的对象，这样便可以通过该对象访问方法区中的这些数据。

#### 连接
##### 验证：确保被加载的类的正确性

验证是连接阶段的第一步，这一阶段的目的是为了确保Class文件的字节流中包含的信息符合当前虚拟机的要求，并且不会危害虚拟机自身的安全。验证阶段大致会完成4个阶段的检验动作：

* 文件格式验证：验证字节流是否符合Class文件格式的规范；例如：是否以 0xCAFEBABE开头、主次版本号是否在当前虚拟机的处理范围之内、常量池中的常量是否有不被支持的类型。

* 元数据验证：对字节码描述的信息进行语义分析（注意：对比javac编译阶段的语义分析），以保证其描述的信息符合Java语言规范的要求；例如：这个类是否有父类，除了 java.lang.Object之外。

* 字节码验证：通过数据流和控制流分析，确定程序语义是合法的、符合逻辑的。

* 符号引用验证：确保解析动作能正确执行。

验证阶段是非常重要的，但不是必须的，它对程序运行期没有影响，如果所引用的类经过反复验证，那么可以考虑采用 -Xverifynone参数来关闭大部分的类验证措施，以缩短虚拟机类加载的时间。

##### 准备：为类的 静态变量分配内存，并将其初始化为默认值

准备阶段是正式为类变量分配内存并设置类变量初始值的阶段，这些内存都将在方法区中分配。对于该阶段有以下几点需要注意：

```
1、这时候进行内存分配的仅包括类变量（static），而不包括实例变量，实例变量会在对象实例化时随着对象一块分配在Java堆中。

2、这里所设置的初始值通常情况下是数据类型默认的零值（如0、0L、null、false等），而不是被在Java代码中被显式地赋予的值。

3、如果类字段的字段属性表中存在 ConstantValue属性，即同时被final和static修饰，那么在准备阶段变量value就会被初始化为ConstValue属性所指定的值。
```

假设一个类变量的定义为： public static int value=3；

>那么变量value在准备阶段过后的初始值为0，而不是3，因为这时候尚未开始执行任何Java方法，而把value赋值为3的 public static指令是在程序编译后，存放于类构造器 <clinit>（）方法之中的，所以把value赋值为3的动作将在初始化阶段才会执行。

假设上面的类变量value被定义为： public static final int value=3；

>编译时Javac将会为value生成ConstantValue属性，在准备阶段虚拟机就会根据 ConstantValue的设置将value赋值为3。我们可以理解为static final常量在编译期就将其结果放入了调用它的类的常量池中

这里还需要注意如下几点：

```
对基本数据类型来说，对于类变量（static）和全局变量，如果不显式地对其赋值而直接使用，则系统会为其赋予默认的零值，而对于局部变量来说，在使用前必须显式地为其赋值，否则编译时不通过。

对于同时被static和final修饰的常量，必须在声明的时候就为其显式地赋值，否则编译时不通过；而只被final修饰的常量则既可以在声明时显式地为其赋值，也可以在类初始化时显式地为其赋值，总之，在使用前必须为其显式地赋值，系统不会为其赋予默认零值。

对于引用数据类型reference来说，如数组引用、对象引用等，如果没有对其进行显式地赋值而直接使用，系统都会为其赋予默认的零值，即null。

如果在数组初始化时没有对数组中的各元素赋值，那么其中的元素将根据对应的数据类型而被赋予默认的零值。


```

##### 解析：把类中的符号引用转换为直接引用

解析阶段是虚拟机将常量池内的符号引用替换为直接引用的过程，解析动作主要针对类或接口、字段、类方法、接口方法、方法类型、方法句柄和调用点限定符7类符号引用进行。符号引用就是一组符号来描述目标，可以是任何字面量。

直接引用就是直接指向目标的指针、相对偏移量或一个间接定位到目标的句柄。

##### 初始化

初始化，为类的静态变量赋予正确的初始值，JVM负责对类进行初始化，主要对类变量进行初始化。在Java中对类变量进行初始值设定有两种方式：

* ①声明类变量是指定初始值

* ②使用静态代码块为类变量指定初始值

JVM初始化步骤

```
1、假如这个类还没有被加载和连接，则程序先加载并连接该类

2、假如该类的直接父类还没有被初始化，则先初始化其直接父类

3、假如类中有初始化语句，则系统依次执行这些初始化语句
```

类初始化时机：

只有当对类的主动使用的时候才会导致类的初始化，类的主动使用包括以下六种：

```
创建类的实例，也就是new的方式

访问某个类或接口的静态变量，或者对该静态变量赋值

调用类的静态方法

反射（如 Class.forName(“com.shengsiyuan.Test”)）

初始化某个类的子类，则其父类也会被初始化

Java虚拟机启动时被标明为启动类的类（ JavaTest），直接使用 java.exe命令来运行某个主类
```

##### 结束生命周期

在如下几种情况下，Java虚拟机将结束生命周期

```
执行了 System.exit()方法

程序正常执行结束

程序在执行过程中遇到了异常或错误而异常终止

由于操作系统出现错误而导致Java虚拟机进程终止
```

#### 类加载器
寻找类加载器，先来一个小例子

```
package com.neo.classloader;
public class ClassLoaderTest {
     public static void main(String[] args) {
        ClassLoader loader = Thread.currentThread().getContextClassLoader();
        System.out.println(loader);
        System.out.println(loader.getParent());
        System.out.println(loader.getParent().getParent());
    }
}
```

运行后，输出结果：

```
sun.misc.Launcher$AppClassLoader@64fef26a
sun.misc.Launcher$ExtClassLoader@1ddd40f3
null
```

从上面的结果可以看出，并没有获取到 ExtClassLoader的父Loader，原因是 BootstrapLoader（引导类加载器）是用C语言实现的，找不到一个确定的返回父Loader的方式，于是就返回null。

这几种类加载器的层次关系如下图所示：

![](http://mmbiz.qpic.cn/mmbiz_png/PgqYrEEtEnokxXiapDdvntH8PGwa0zGXM7qXKib1ibsib1BuyLxjoP1sgorwib78yTD4896N5r1AibdhDXTHZ7z9VyBg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

注意：这里父类加载器并不是通过继承关系来实现的，而是采用组合实现的。

站在Java虚拟机的角度来讲，只存在两种不同的类加载器：启动类加载器：它使用C++实现（这里仅限于Hotspot，也就是JDK1.5之后默认的虚拟机，有很多其他的虚拟机是用Java语言实现的），是虚拟机自身的一部分；所有其它的类加载器：这些类加载器都由Java语言实现，独立于虚拟机之外，并且全部继承自抽象类 java.lang.ClassLoader，这些类加载器需要由启动类加载器加载到内存中之后才能去加载其他的类。

站在Java开发人员的角度来看，类加载器可以大致划分为以下三类：

启动类加载器：
>BootstrapClassLoader，负责加载存放在 JDK\jre\lib(JDK代表JDK的安装目录，下同)下，或被 -Xbootclasspath参数指定的路径中的，并且能被虚拟机识别的类库（如rt.jar，所有的java.开头的类均被 BootstrapClassLoader加载）。启动类加载器是无法被Java程序直接引用的。

扩展类加载器：
> ExtensionClassLoader，该加载器由 sun.misc.Launcher$ExtClassLoader实现，它负责加载 JDK\jre\lib\ext目录中，或者由 java.ext.dirs系统变量指定的路径中的所有类库（如javax.开头的类），开发者可以直接使用扩展类加载器。	

应用程序类加载器：
>  ApplicationClassLoader，该类加载器由 sun.misc.Launcher$AppClassLoader来实现，它负责加载用户类路径（ClassPath）所指定的类，开发者可以直接使用该类加载器，如果应用程序中没有自定义过自己的类加载器，一般情况下这个就是程序中默认的类加载器。

应用程序都是由这三种类加载器互相配合进行加载的，如果有必要，我们还可以加入自定义的类加载器。因为JVM自带的ClassLoader只是懂得从本地文件系统加载标准的java class文件，因此如果编写了自己的ClassLoader，便可以做到如下几点：

```
1、在执行非置信代码之前，自动验证数字签名。

2、动态地创建符合用户特定需要的定制化构建类。

3、从特定的场所取得java class，例如数据库中和网络中。
```

##### JVM类加载机制

全盘负责，

```
当一个类加载器负责加载某个Class时，
该Class所依赖的和引用的其他Class也将由该类加载器负责载入，
除非显示使用另外一个类加载器来载入
```

父类委托，

```
先让父类加载器试图加载该类，
只有在父类加载器无法加载该类时才尝试从自己的类路径中加载该类
```

缓存机制，

```
缓存机制将会保证所有加载过的Class都会被缓存，
当程序中需要使用某个Class时，类加载器先从缓存区寻找该Class，
只有缓存区不存在，系统才会读取该类对应的二进制数据，并将其转换成Class对象，存入缓存区。
这就是为什么修改了Class后，必须重启JVM，程序的修改才会生效
```

#### 类的加载

类加载有三种方式：

```
1、命令行启动应用时候由JVM初始化加载

2、通过Class.forName()方法动态加载

3、通过ClassLoader.loadClass()方法动态加载
```

```
package com.neo.classloader;
public class loaderTest { 
        public static void main(String[] args) throws ClassNotFoundException { 
                ClassLoader loader = HelloWorld.class.getClassLoader(); 
                System.out.println(loader); 
                //使用ClassLoader.loadClass()来加载类，不会执行初始化块 
                loader.loadClass("Test2"); 
                //使用Class.forName()来加载类，默认会执行初始化块 
//                Class.forName("Test2"); 
                //使用Class.forName()来加载类，并指定ClassLoader，初始化时不执行静态块 
//                Class.forName("Test2", false, loader); 
        } 
}
```

* Class.forName()和ClassLoader.loadClass()区别

Class.forName()：

```
将类的.class文件加载到jvm中之外，还会对类进行解释，执行类中的static块；
```

ClassLoader.loadClass()：

```
只干一件事情，就是将.class文件加载到jvm中，不会执行static中的内容,只有在newInstance才会去执行static块。
```

Class.forName(name,initialize,loader)带参函数也可控制是否加载static块。并且只有调用了newInstance()方法采用调用构造函数，创建类的对象 。

#### 双亲委派模型

双亲委派模型的工作流程是：
>如果一个类加载器收到了类加载的请求，它首先不会自己去尝试加载这个类，而是把请求委托给父加载器去完成，依次向上，因此，所有的类加载请求最终都应该被传递到顶层的启动类加载器中，只有当父加载器在它的搜索范围中没有找到所需的类时，即无法完成该加载，子加载器才会尝试自己去加载该类。

双亲委派机制:

```
1、当 AppClassLoader加载一个class时，它首先不会自己去尝试加载这个类，而是把类加载请求委派给父类加载器ExtClassLoader去完成。

2、当 ExtClassLoader加载一个class时，它首先也不会自己去尝试加载这个类，而是把类加载请求委派给BootStrapClassLoader去完成。

3、如果 BootStrapClassLoader加载失败（例如在 $JAVA_HOME/jre/lib里未查找到该class），会使用 ExtClassLoader来尝试加载；

4、若ExtClassLoader也加载失败，则会使用 AppClassLoader来加载，如果 AppClassLoader也加载失败，则会报出异常 ClassNotFoundException。
```

ClassLoader源码分析：

```
public Class<?> loadClass(String name)throws ClassNotFoundException {
            return loadClass(name, false);
    }
    
    protected synchronized Class<?> loadClass(String name, boolean resolve)throws ClassNotFoundException {
            // 首先判断该类型是否已经被加载
            Class c = findLoadedClass(name);
            if (c == null) {
                //如果没有被加载，就委托给父类加载或者委派给启动类加载器加载
                try {
                    if (parent != null) {
                         //如果存在父类加载器，就委派给父类加载器加载
                        c = parent.loadClass(name, false);
                    } else {
                    //如果不存在父类加载器，就检查是否是由启动类加载器加载的类，通过调用本地方法native Class findBootstrapClass(String name)
                        c = findBootstrapClass0(name);
                    }
                } catch (ClassNotFoundException e) {
                 // 如果父类加载器和启动类加载器都不能完成加载任务，才调用自身的加载功能
                    c = findClass(name);
                }
            }
            if (resolve) {
                resolveClass(c);
            }
            return c;
        }
```

双亲委派模型意义：

```
-系统类防止内存中出现多份同样的字节码

-保证Java程序安全稳定运行
```

#### 自定义类加载器

通常情况下，我们都是直接使用系统类加载器。但是，有的时候，我们也需要自定义类加载器。
比如应用是通过网络来传输 Java类的字节码，为保证安全性，这些字节码经过了加密处理，这时系统类加载器就无法对其进行加载，这样则需要自定义类加载器来实现。自定义类加载器一般都是继承自 ClassLoader类，从上面对 loadClass方法来分析来看，我们只需要重写 findClass 方法即可。下面我们通过一个示例来演示自定义类加载器的流程：

```
package com.neo.classloader;

import java.io.*;


public class MyClassLoader extends ClassLoader {

    private String root;

    protected Class<?> findClass(String name) throws ClassNotFoundException {
        byte[] classData = loadClassData(name);
        if (classData == null) {
            throw new ClassNotFoundException();
        } else {
            return defineClass(name, classData, 0, classData.length);
        }
    }

    private byte[] loadClassData(String className) {
        String fileName = root + File.separatorChar
                + className.replace('.', File.separatorChar) + ".class";
        try {
            InputStream ins = new FileInputStream(fileName);
            ByteArrayOutputStream baos = new ByteArrayOutputStream();
            int bufferSize = 1024;
            byte[] buffer = new byte[bufferSize];
            int length = 0;
            while ((length = ins.read(buffer)) != -1) {
                baos.write(buffer, 0, length);
            }
            return baos.toByteArray();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }

    public String getRoot() {
        return root;
    }

    public void setRoot(String root) {
        this.root = root;
    }

    public static void main(String[] args)  {

        MyClassLoader classLoader = new MyClassLoader();
        classLoader.setRoot("E:\\temp");

        Class<?> testClass = null;
        try {
            testClass = classLoader.loadClass("com.neo.classloader.Test2");
            Object object = testClass.newInstance();
            System.out.println(object.getClass().getClassLoader());
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
    }
}
```

自定义类加载器的核心在于对字节码文件的获取，如果是加密的字节码则需要在该类中对文件进行解密。由于这里只是演示，我并未对class文件进行加密，因此没有解密的过程。这里有几点需要注意：

```
1、这里传递的文件名需要是类的全限定性名称，即 com.paddx.test.classloading.Test格式的，因为 defineClass 方法是按这种格式进行处理的。

2、最好不要重写loadClass方法，因为这样容易破坏双亲委托模式。

3、这类Test 类本身可以被 AppClassLoader类加载，因此我们不能把 com/paddx/test/classloading/Test.class放在类路径下。否则，由于双亲委托机制的存在，会直接导致该类由 AppClassLoader加载，而不会通过我们自定义类加载器来加载。
```












