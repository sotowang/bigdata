[TOC]



# 腾讯IEG（offer）

## 一面（全程问基础）：

```
1、介绍项目
2、String、StringBuffer、StringBuilder的区别，怎么理解String不变性
3、==和equals的区别，如果重写了equals()不重写hashCode()会发生什么
4、volatile怎么保证可见性，synchronized和lock的区别，synchronized的底层实现
5、sleep和wait的区别，sleep会不会释放锁，notify和notifyAll的区别
6、了不了解线程的局部变量，讲讲线程池参数
7、什么情况会发生死锁，死锁的处理方法
8、Cookie和Session的区别，怎么防止Cookie欺骗
9、从用户在浏览器输入域名，到浏览器显示出页面的过程
```

---

### String、StringBuffer、StringBuilder的区别，怎么理解String不变性

[java中String、StringBuffer、StringBuilder的区别](https://www.cnblogs.com/xudong-bupt/p/3961159.html)

**1.可变与不可变**

　　String类中使用字符数组保存字符串，如下就是，因为有“final”修饰符，所以可以知道string对象是不可变的。

　　　　**private final char value[];**

　　StringBuilder与StringBuffer都继承自AbstractStringBuilder类，在AbstractStringBuilder中也是使用字符数组保存字符串，如下就是，可知这两种对象都是可变的。

　　　　**char[] value;**

**2.是否多线程安全**

　　String中的对象是不可变的，也就可以理解为常量，**显然线程安全**。

　　AbstractStringBuilder是StringBuilder与StringBuffer的公共父类，定义了一些字符串的基本操作，如expandCapacity、append、insert、indexOf等公共方法。

　　StringBuffer对方法加了同步锁或者对调用的方法加了同步锁，所以是**线程安全的**。看如下源码：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```java
1 public synchronized StringBuffer reverse() {
2     super.reverse();
3     return this;
4 }
5 
6 public int indexOf(String str) {
7     return indexOf(str, 0);        //存在 public synchronized int indexOf(String str, int fromIndex) 方法
8 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　StringBuilder并没有对方法进行加同步锁，所以是**非线程安全的**。

 **3.StringBuilder与StringBuffer共同点**

　　StringBuilder与StringBuffer有公共父类AbstractStringBuilder(**抽象类**)。

　　抽象类与接口的其中一个区别是：抽象类中可以定义一些子类的公共方法，子类只需要增加新的功能，不需要重复写已经存在的方法；而接口中只是对方法的申明和常量的定义。

　　StringBuilder、StringBuffer的方法都会调用AbstractStringBuilder中的公共方法，如super.append(...)。只是StringBuffer会在方法上加synchronized关键字，进行同步。

　　**最后，如果程序不是多线程的，那么使用StringBuilder效率高于StringBuffer。**

---

[Java基础之String、StringBuffer与StringBuilder ](https://blog.csdn.net/chenliguan/article/details/51911906)

#### 1 区别

* String对象是常量，它的值不能被创建后改变，StringBuffer和StringBuilder可以可变；
* StringBuilder非线程安全（单线程使用），String与StringBuffer线程安全（多线程使用）；
* 如果程序不是多线程的，那么使用StringBuilder效率高于StringBuffer。

（2）常量池

常量池是一个内存空间，不同于使用new关键字创建的对象所在的堆空间。
常量池是为了避免频繁的创建和销毁对象而影响系统性能，其实现了对象的共享。当需要一个对象时，就可以从池中取一个出来（如果池中没有则创建一个），则在需要重复创建相等变量时节省了很多时间。
在编译期被确定，并被保存在已编译的.class文件中的一些数据，包括类、方法、接口等中的常量和字符串常量。常量池还具备动态性，运行期间可以将新的常量放入池中。java中基本类型的包装类的大部分都实现了常量池技术， 即Byte,Short,Integer,Long,Character,Boolean；
（3）下面代码创建了几个String对象?

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

#### 2 String与StringBuffer区别

##### 2.1 在修改时对象自身是否可变（主要区别）

（1） String在修改时不会改变对象自身
　　在每次对 String 类型进行改变的时候其实都等同于生成了一个新的 String 对象，然后将指针指向新的 String 对象，所以经常改变内容的字符串最好不要用 String 。

```java
String str = "abc";//地址str1s
str = "def";//地址str2
```

（2） StringBuffer在修改时会改变对象自身

　　每次结果都会对 StringBuffer 对象本身进行操作，而不是生成新的对象，再改变对象引用。所以在一般情况下我们推荐使用 StringBuffer ，特别是字符串对象经常改变的情况下。StringBuffer 上的主要操作是 append 和 insert 方法。

```java
StringBuffer strBuffer = new StringBuffer("abc");//地址strBuffer,值是abc
strBuffer.append("def");//地址strBuffer,值是abcdef
```

#### 3 总结

（1）如果要操作少量的数据用 String；
（2）多线程操作字符串缓冲区下操作大量数据 StringBuffer；
（3）单线程操作字符串缓冲区下操作大量数据 StringBuilder。

---
### ==和equals的区别，如果重写了equals()不重写hashCode()会发生什么

[hashcode和equals为何要同时重写](https://blog.csdn.net/woailuo453786790/article/details/49779869)

首先equals与hashcode间的关系是这样的：

 1、如果两个对象相同（即用equals比较返回true），那么它们的hashCode值一定要相同；

 2、如果两个对象的hashCode相同，它们并不一定相同(即用equals比较返回false)   

自我的理解：由于为了提高程序的效率才实现了hashcode方法，先进行hashcode的比较，如果不同，那没就不必在进行equals的比较了，这样就大大减少了equals比较的次数，这对比需要比较的数量很大的效率提高是很明显的，一个很好的例子就是在集合中的使用；

> 我们都知道java中的List集合是有序的，因此是可以重复的，而set集合是无序的，因此是不能重复的，那么怎么能保证不能被放入重复的元素呢，但靠equals方法一样比较的话，如果原来集合中以后又10000个元素了，那么放入10001个元素，难道要将前面的所有元素都进行比较，看看是否有重复，欧码噶的，这个效率可想而知，因此hashcode就应遇而生了，java就采用了hash表，利用哈希算法（也叫散列算法），就是将对象数据根据该对象的特征使用特定的算法将其定义到一个地址上，那么在后面定义进来的数据只要看对应的hashcode地址上是否有值，那么就用equals比较，如果没有则直接插入，只要就大大减少了equals的使用次数，执行效率就大大提高了。

继续上面的话题，为什么必须要重写hashcode方法，其实简单的说就是为了保证同一个对象，保证在equals相同的情况下hashcode值必定相同，如果重写了equals而未重写

hashcode方法，可能就会出现两个没有关系的对象equals相同的（因为equal都是根据对象的特征进行重写的），但hashcode确实不相同的

[==和equals的区别，如果重写了equals()不重写hashCode()会发生什么](https://blog.csdn.net/javazejian/article/details/51348320)

[==和equals的区别，如果重写了equals()不重写hashCode()会发生什么](https://blog.csdn.net/neosmith/article/details/17068365)

[==和equals的区别，如果重写了equals()不重写hashCode()会发生什么](https://blog.csdn.net/u013679744/article/details/57074669)

---
### volatile怎么保证可见性

[4.4 volatile关键字是如何保证可见性的](http://www.tianshouzhi.com/api/tutorials/mutithread/286)

在前面我们提到volatile关键字可以保证多个线程运行时的可见性问题。在单核CPU的情况下，是不存在可见性问题的，如果是多核CPU，可见性问题就会暴露出来。

我们知道线程中运行的代码最终都是交给CPU执行的，而代码执行时所需使用到的数据来自于内存(或者称之为主存)。但是CPU是不会直接操作内存的，每个CPU都会有自己的缓存，操作缓存的速度比操作主存更快。

因此当某个线程需要修改一个数据时，事实上步骤是如下的：

```
1、将主存中的数据加载到缓存中

2、CPU对缓存中的数据进行修改

3、将修改后的值刷新到内存中
```

多个线程操作同一个变量的情况，则可以用下图表示:

![可见性.png](http://static.tianshouzhi.com/ueditor/upload/image/20160610/1465533403406066640.png)

第一步：线程1、线程2、线程3操作的是主存中的同一个变量，并且分别交由CPU1、CPU2、CPU3处理。

第二步：3个CPU分别将主存中变量加载到缓存中

第三步：各自将修改后的值刷新到主存总

问题就出现在第二步，因为每个CPU操作的是各自的缓存，所以不同的CPU之间是无法感知其他CPU对这个变量的修改的，最终就可能导致结果与我们的预期不符。

而使用了volatile关键字之后，情况就有所不同，volatile关键字有两层语义：

```
1、立即将缓存中数据写会到内存中

2、其他处理器通过嗅探总线上传播过来了数据监测自己缓存的值是不是过期了，如果过期了，就会对应的缓存中的数据置为无效。而当处理器对这个数据进行修改时，会重新从内存中把数据读取到缓存中进行处理。
```

在这种情况下，不同的CPU之间就可以感知其他CPU对变量的修改，并重新从内存中加载更新后的值，因此可以解决可见性问题。

---

### [并发编程的锁机制：synchronized和lock](https://juejin.im/post/5a43ad786fb9a0450909cb5f)

#### 1.1 可重入锁

如果锁具备可重入性，则称作为可重入锁。synchronized和ReentrantLock都是可重入锁，可重入性在我看来实际上表明了锁的分配机制：基于线程的分配，而不是基于方法调用的分配。举比如说，当一个线程执行到method1 的synchronized方法时，而在method1中会调用另外一个synchronized方法method2，此时该线程不必重新去申请锁，而是可以直接执行方法method2。

#### 1.2 读写锁

读写锁将对一个资源的访问分成了2个锁，如文件，一个读锁和一个写锁。正因为有了读写锁，才使得多个线程之间的读操作不会发生冲突。`ReadWriteLock`就是读写锁，它是一个接口，ReentrantReadWriteLock实现了这个接口。可以通过readLock()获取读锁，通过writeLock()获取写锁。

#### 1.3 可中断锁

可中断锁，即可以中断的锁。在Java中，synchronized就不是可中断锁，而Lock是可中断锁。 如果某一线程A正在执行锁中的代码，另一线程B正在等待获取该锁，可能由于等待时间过长，线程B不想等待了，想先处理其他事情，我们可以让它中断自己或者在别的线程中中断它，这种就是可中断锁。

Lock接口中的lockInterruptibly()方法就体现了Lock的可中断性。

#### 1.4 公平锁

公平锁即尽量以请求锁的顺序来获取锁。同时有多个线程在等待一个锁，当这个锁被释放时，等待时间最久的线程（最先请求的线程）会获得该锁，这种就是公平锁。

非公平锁即无法保证锁的获取是按照请求锁的顺序进行的，这样就可能导致某个或者一些线程永远获取不到锁。

`synchronized`是非公平锁，它无法保证等待的线程获取锁的顺序。对于`ReentrantLock`和`ReentrantReadWriteLock`，默认情况下是非公平锁，但是可以设置为公平锁。

####  3. 两种锁的比较

##### 3.1 synchronized和lock的区别

- Lock是一个接口，而synchronized是Java中的关键字，synchronized是内置的语言实现；
- synchronized在发生异常时，会自动释放线程占有的锁，因此不会导致死锁现象发生；而Lock在发生异常时，如果没有主动通过unLock()去释放锁，则很可能造成死锁现象，因此使用Lock时需要在finally块中释放锁；
- Lock可以让等待锁的线程响应中断，而synchronized却不行，使用synchronized时，等待的线程会一直等待下去，不能够响应中断；
- 通过Lock可以知道有没有成功获取锁，而synchronized却无法办到。
- Lock可以提高多个线程进行读操作的效率。（可以通过readwritelock实现读写分离）
- 性能上来说，在资源竞争不激烈的情形下，Lock性能稍微比synchronized差点（编译程序通常会尽可能的进行优化synchronized）。但是当同步非常激烈的时候，synchronized的性能一下子能下降好几十倍。而ReentrantLock确还能维持常态。

 到了JDK1.6，发生了变化，对synchronize加入了很多优化措施，有自适应自旋，锁消除，锁粗化，轻量级锁，偏向锁等等。导致在JDK1.6上synchronize的性能并不比Lock差。官方也表示，他们也更支持synchronize，在未来的版本中还有优化余地，所以还是提倡在synchronized能实现需求的情况下，优先考虑使用synchronized来进行同步。

---

### [synchronized的JVM底层实现（很详细 很底层）](https://blog.csdn.net/niuwei22007/article/details/51433669)

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

---

### sleep和wait的区别，sleep会不会释放锁

[java sleep和wait的区别的疑惑?](https://www.zhihu.com/question/23328075)

![](https://pic2.zhimg.com/80/bb4f380c79779c9dc1aea6f0a6c10b6d_hd.jpg)

**sleep** 是让线程进入阻塞状态，一定时间之后回到非阻塞状态，从而可以重新获得 CPU。

**wait** 调用的时候需要先获得该 Object 的锁，调用 wait 后，会把当前的锁释放掉同时阻塞住；当别的线程调用该 Object 的 notify/notifyAll 之后，有可能得到 CPU，同时重新获得锁。由于有如上描述锁的设计，只要在 notify 的时候首先获得锁，就可以保证 notify 的时候或者处于 wait 线程获得锁之前，或者正在 wait，从而保证不会丢掉这次 notify 信息。

---

### [java中的notify和notifyAll有什么区别？](https://www.zhihu.com/question/37601861)

[notify和notifyAll的区别](https://www.zhihu.com/question/37601861)

先说两个概念：锁池和等待池

- 锁池: 假设线程A已经拥有了某个对象(注意:不是类)的锁，而其它的线程想要调用这个对象的某个synchronized方法(或者synchronized块)，由于这些线程在进入对象的synchronized方法之前必须先获得该对象的锁的拥有权，但是该对象的锁目前正被线程A拥有，所以这些线程就进入了该对象的锁池中。
- 等待池: 假设一个线程A调用了某个对象的wait()方法，线程A就会释放该对象的锁后，进入到了该对象的等待池中

> Reference：[java中的锁池和等待池 ](https://link.zhihu.com/?target=http%3A//blog.csdn.net/emailed/article/details/4689220)

然后再来说notify和notifyAll的区别

-  如果线程调用了对象的 wait()方法，那么线程便会处于该对象的**等待池**中，等待池中的线程**不会去竞争该对象的锁**。
- 当有线程调用了对象的 **notifyAll**()方法（唤醒所有 wait 线程）或 **notify**()方法（只随机唤醒一个 wait 线程），被唤醒的的线程便会进入该对象的锁池中，锁池中的线程会去竞争该对象锁。也就是说，调用了notify后只要一个线程会由等待池进入锁池，而notifyAll会将该对象等待池内的所有线程移动到锁池中，等待锁竞争
- 优先级高的线程竞争到对象锁的概率大，假若某线程没有竞争到该对象锁，它**还会留在锁池中**，唯有线程再次调用 wait()方法，它才会重新回到等待池中。而竞争到对象锁的线程则继续往下执行，直到执行完了 synchronized 代码块，它会释放掉该对象锁，这时锁池中的线程会继续竞争该对象锁。

> Reference：[线程间协作：wait、notify、notifyAll ](https://link.zhihu.com/?target=http%3A//wiki.jikexueyuan.com/project/java-concurrency/collaboration-between-threads.html)

综上，所谓唤醒线程，另一种解释可以说是将线程由等待池移动到锁池，notifyAll调用后，会将全部线程由等待池移到锁池，然后参与锁的竞争，竞争成功则继续执行，如果不成功则留在锁池等待锁被释放后再次参与竞争。而notify只会唤醒一个线程。

有了这些理论基础，后面的notify可能会导致死锁，而notifyAll则不会的例子也就好解释了

---

### 什么情况会发生死锁，死锁的处理方法

[死锁产生的条件和处理死锁的方法](https://blog.csdn.net/a1414345/article/details/70215628)

**死锁（Deadlock）**就是指两个或两个以上的进程在执行的过程中，由于竞争资源或者由于彼此通信而造成的一种阻塞的现象，若无外力作用，它们都将无法推进下去，此时称系统处于死锁状态或系统产生了死锁，这些永远在互相等待的进程称为死锁进程。

死锁发生的条件
若可能发生死锁，则必定要出现三个条件：

1. 互斥条件： 指进程对所分配的资源进行排它性使用，即在一段时间内某资源只由一个进程占用。如果此时还有其它进程请求资源，则请求者只能等待，直到占有资源的进程用完后释放资源。
2. 占有且等待条件： 指进程已经保持至少一个资源，但又提出了新的资源请求，而该资源已被其他进程占有，此时请求进程阻塞，但它不会释放自己已经占有的资源。
3. 非抢占条件： 指进程已经获得的资源，在未使用完之前，不能被剥夺，只能在使用完时由自己释放。

满足以上三个条件可能会产生死锁，但如果只有这三个条件，则不一定产生死锁。对死锁的产生，还需要第四个条件。

4. 循环等待条件 存在一个封闭的进程链，使得每个资源至少占有此链中下一个进程所需要的一个资源。

前三个条件是死锁存在的必要条件，但不是充分条件。第四个条件实际上是前三个条件的潜在结果，即假设前三个条件存在，可能发生的一系列事情会导致不可解的循环等待。这四个条件连在一起构成了死锁的充分必要条件。

#### 死锁处理

有三种方法可以处理死锁。

- **第一种方法**：采用某种策略来消除条件1-4中的一个条件的出现来防止死锁**【死锁预防】**。
- **第二种方法**：基于资源分配的当前状态做动态选择来避免死锁**【死锁避免】**。
- **第三种方法**：试图去检测死锁的存在并试图从死锁中恢复出来**【死锁检测】**

---
### [Cookie和Session的区别](https://blog.csdn.net/baidu_31337243/article/details/48954445)

* cookie 和session 的区别：

1、cookie数据存放在客户的浏览器上，session数据放在[服务器](https://www.baidu.com/s?wd=%E6%9C%8D%E5%8A%A1%E5%99%A8&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)上。

2、cookie不是很安全，别人可以分析存放在本地的COOKIE并进行COOKIE欺骗
   考虑到安全应当使用session。

3、session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能
   考虑到减轻服务器性能方面，应当使用COOKIE。

4、单个cookie保存的数据不能超过4K，很多浏览器都限制一个站点最多保存20个cookie。

5、所以个人建议：
   将登陆信息等重要信息存放为SESSION
   其他信息如果需要保留，可以放在COOKIE中

* Cookie

Cookie是客户端技术，程序把每个用户的数据以cookie的形式写给用户各自的浏览器。当用户使用浏览器再去访问服务器中的web资源时，就会带着各自的数据去。这样，web资源处理的就是用户各自的数据了。 

* Session

Session是服务器端技术，利用这个技术，服务器在运行时可以为每一个用户的浏览器创建一个其独享的session对象，由于session为用户浏览器独享，所以用户在访问服务器的web资源时，可以把各自的数据放在各自的session中，当用户再去访问服务器中的其它web资源时，其它web资源再从用户各自的session中取出数据为用户服务。

---

### 怎么防止Cookie欺骗

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

---
### [从用户在浏览器输入域名，到浏览器显示出页面的过程](https://blog.csdn.net/qq_24147051/article/details/81115806)
---



## 二面（全程怼项目，压力面）：

### [1. 谈谈对UDF的理解，写UDF的目的，代码怎么写的](https://blog.csdn.net/WYpersist/article/details/80314352)
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

## 三面（接着怼项目）：

```
1、看你写了实时计算的程序，你怎么保证计算的结果肯定是对的
2、数据接入的时候，怎么往kafka topic里面发的，用的什么方式，起了几个线程，producer是线程安全的吗
3、kafka集群有几台机器，怎么确定你们项目需要用几台机器，有评估过吗，吞吐量测过吗
4、spark streaming是怎么跟kafka交互的，具体代码怎么写的，程序执行流程是怎样的，这个过程中怎么确保数据不丢
5、kafka监控是怎么做的，kafka中能彻底删除数据吗，怎么做的
```

## 面委会（全程聊天）：
* 平时是怎么学习的，爱看哪些博客，怎么看待加班，有没有成为leader的潜力

# 网易考拉（offer）
## 一面：

```
1、sql题：学生成绩表，把每科最高分前三名统计出来
2、算法题：二维数组中的查找
3、kafka如何保证高吞吐的，了不了解kafka零拷贝，具体怎么做的
4、sql有几种join，map join了解过没
5、hbase中row key该怎么设计
6、hdfs文件上传流程，hdfs的容错机制
7、怎么解决hive数据倾斜问题
```

## 二面（全程写写写）：

```
1、算法题：二维矩阵相乘
2、算法题：链表中环的入口
3、写一下mysql binlog的数据格式，怎么进行数据清洗的
4、写一个正则表达式进行手机号匹配
5、讲一下数据仓库层级的划分，每层的作用
```

# 美团新到店（offer）
* 去了北京美团公司里面试，一上午面完，第二天通知高分通过
## 一面（简单的聊了聊，10min）：

```
1、介绍项目，以及滴滴的实习经历
2、JVM内存的划分
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

# 小米（offer）
## 一面：

```java
1、java和python的区别，对面向对象的理解，和面向过程相比有什么区别
2、java为什么不能多继承
3、讲一下java抽象类和接口
4、java中为什么要写非static方法
5、volatile和synchronized的区别
6、算法题：跳台阶问题
7、算法题：树的非递归后序遍历
8、设计题：一个停车场有一些大车位和小车位，大车只能停大车位，小车既能停大车位又能停小车位，实现这种场景下的调度系统
```

## 二面：

```
1、算法题：输入一个字符串，输出该字符串中字符的所有排列
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

--------------------------写的头疼，明天接着更-------------------------------------------
# 完美世界（offer）

# 京东广告部（四面完没了消息）

# 阿里菜鸟（三面完已回绝）
* 阿里的面试还是比较重视基础的，应该是bat里面问基础问的最多的

## 一面：

```
1、HashMap和HashTable的区别，HashMap怎么解决hash冲突，jdk1.8后对HashMap的改进
2、讲讲ConcurrentHashMap，ConcurrentHashMap怎么保证线程安全，HashTable怎么保证线程安全
3、HashSet的底层实现，是不是线程安全的
4、ArrayList和LinkedList的区别，是不是线程安全的
5、讲讲设计模式，最常用哪种设计模式，单例模式的实现方式
6、进程和线程，Java实现多线程的方式，什么是线程安全，怎么保证多线程线程安全
7、可重入锁的可重入性是什么意思，哪些是可重入锁
8、为什么要用线程池，线程池的好处
9、JVM垃圾处理方法，对象什么时候进入老年代，什么时候进行FullGC
10、Java堆溢出问题怎么处理，内存泄漏和内存溢出的区别
11、智力题：50个红球和50个黑球往两个桶里放，然后自己去抽，怎么样才能使抽到红球的概率最高
```

---
### Java堆溢出问题怎么处理，内存泄漏和内存溢出的区别
[Java堆溢出问题怎么处理，内存泄漏和内存溢出的区别](https://blog.csdn.net/sinat_35512245/article/details/54866068)

[Java内存泄漏与内存溢出详解](https://blog.csdn.net/sinat_35512245/article/details/54866068)

* 内存泄漏指你用malloc或new申请了一块内存，但是没有通过free或delete将内存释放，导致这块内存一直处于占用状态。

* 内存溢出指你申请了10个字节的空间，但是你在这个空间写入11或以上字节的数据，就是溢出。

内存泄露是指程序中间动态分配了内存，但在程序结束时没有释放这部分内存，从而造成那部分内存不可用的情况，重启计算机可以解决，但也有可能再次发生内存泄露，内存泄露和硬件没有关系，它是由软件设计缺陷引起的。

内存泄漏可以分为4类： 

* 1)常发性内存泄漏。
>发生内存泄漏的代码会被多次执行到，每次被执行的时候都会导致一块内存泄漏。

* 2)偶发性内存泄漏。
>发生内存泄漏的代码只有在某些特定环境或操作过程下才会发生。常发性和偶发性是相对的。对于特定的环境，偶发性的也许就变成了常发性的。所以测试环境和测试方法对检测内存泄漏至关重要。

* 3)一次性内存泄漏。
> 发生内存泄漏的代码只会被执行一次，或者由于算法上的缺陷，导致总会有一块仅且一块内存发生泄漏。比如，在类的构造函数中分配内存，在析构函数中却没有释放该内存，所以内存泄漏只会发生一次。

* 4)隐式内存泄漏。
>程序在运行过程中不停的分配内存，但是直到结束的时候才释放内存。严格的说这里并没有发生内存泄漏，因为最终程序释放了所有申请的内存。但是对于一个服务器程序，需要运行几天，几周甚至几个月，不及时释放内存也可能导致最终耗尽系统的所有内存。所以，我们称这类内存泄漏为隐式内存泄漏。

内存溢出即用户在对其数据缓冲区操作时，超过了其缓冲区的边界；尤其是对缓冲区写操作时，缓冲区的溢出很可能导致程序的异常。

* Java内存泄露与溢出的区别

```
内存溢出就是你要求分配的内存超出了系统能给你的，系统不能满足需求，于是产生溢出。

Java内存泄漏就是没有及时清理内存垃圾，导致系统无法再给你提供内存资源（内存资源耗尽）。
```

看到上面的解释，可能有些朋友还是不太理解吧。没问题，看以下例子:

* 1.Java内存泄露是说程序逻辑问题,造成申请的内存无法释放.这样的话无论多少内存,早晚都会被占用光的. 最简单的例子就是死循环了.由于程序判断错误导经常发生此事。

* 2.Java内存泄漏是指在堆上分配的内存没有被释放，从而失去对其控制。这样会造成程序能使用的内存越来越少，导致系统运行速度减慢，严重情况会使程序当掉。

* 3.关于内存溢出有点出入。比如说你申请了一个integer,但给它存了long才能存下的数，那就是内存溢出。

举个现实中的例子： 
>比如有一个桶，装满了水.你丢个苹果进去。桶的水正常。如果你放个大石头。水就出溢出，内存溢出也就是这个原理。 

区别：

* 内存溢出，提供的内存不够；
* Java内存泄漏，无法再提供内存资源。

### 相关问题


Q:Java中会存在内存泄漏吗？ 	
A:  	Java中也存在内存泄露。	当被分配的对象可达但已无用（未对作废数据内存单元的引用置null）即会引起。

Q: 如何避免内存泄露、溢出？ 	
A: 
* 1)尽早释放无用对象的引用。	
好的办法是使用临时变量的时候，让引用变量在退出活动域后自动设置为null，暗示垃圾收集器来收集该对象，防止发生内存泄露。

* 2)程序进行字符串处理时，尽量避免使用String，而应使用StringBuffer。

[String,StringBuffer与StringBuilder的区别??](https://blog.csdn.net/rmn190/article/details/1492013)

```
简要的说， String 类型和 StringBuffer 类型的主要性能区别其实在于 String 是不可变的对象, 因此在每次对 String 类型进行改变的时候其实都等同于生成了一个新的 String 对象，
然后将指针指向新的 String 对象，所以经常改变内容的字符串最好不要用 String ，因为每次生成对象都会对系统性能产生影响，特别当内存中无引用对象多了以后， JVM 的 GC 就会开始工作，那速度是一定会相当慢的。

 而如果是使用 StringBuffer 类则结果就不一样了，每次结果都会对 StringBuffer 对象本身进行操作，而不是生成新的对象，再改变对象引用。
 所以在一般情况下我们推荐使用 StringBuffer ，特别是字符串对象经常改变的情况下。
 而在某些特别情况下， String 对象的字符串拼接其实是被 JVM 解释成了 StringBuffer 对象的拼接，所以这些时候 String 对象的速度并不会比 StringBuffer 对象慢，
 而特别是以下的字符串对象生成中， String 效率是远要比 StringBuffer 快的：
 String S1 = “This is only a” + “ simple” + “ test”;
 StringBuffer Sb = new StringBuilder(“This is only a”).append(“ simple”).append(“ test”);
 
 你会很惊讶的发现，生成 String S1 对象的速度简直太快了，而这个时候 StringBuffer 居然速度上根本一点都不占优势。其实这是 JVM 的一个把戏，在 JVM 眼里，这个
 
String S1 = “This is only a” + “ simple” + “test”; 其实就是：
String S1 = “This is only a simple test”; 
所以当然不需要太多的时间了。但大家这里要注意的是，如果你的字符串是来自另外的 String 对象的话，速度就没那么快了，譬如：
String S2 = “This is only a”;
String S3 = “ simple”;
String S4 = “ test”;
String S1 = S2 +S3 + S4;

这时候 JVM 会规规矩矩的按照原来的方式去做
```

* 3) 尽量少用静态变量。因为静态变量是全局的，GC不会回收。

* 4)避免集中创建对象尤其是大对象，如果可以的话尽量使用流操作。

	 5)尽量运用对象池技术以提高系统性能。	
生命周期长的对象拥有生命周期短的对象时容易引发内存泄漏，例如大集合对象拥有大数据量的业务对象的时候，可以考虑分块进行处理，然后解决一块释放一块的策略。

* 6)不要在经常调用的方法中创建对象，尤其是忌讳在循环中创建对象。
可以适当的使用hashtable，vector创建一组对象容器，然后从容器中去取那些对象，而不用每次new之后又丢弃。

* 7) 优化配置。

---
### Java对象池技术

[Java对象池技术](https://www.jianshu.com/p/38c5bccf892f)

#### 为什么用对象池
在 java 中，对象的生命周期包括对象创建、对象使用，对象消失三个时间段，其中对象的使用是对象真正需要存活的时间，不好修改，该用的时候还得使用啊。对象的创建和消失就得好好控制下了。对象的创建是比较费时间的，也许感觉不到，好比一个赋值操作int i=1，也是需要耗时的，在比如构造一个对象，一个数组就更加消耗时间。再说对象的消除，在 java 里面使用 GC 来进行对象回收，其实也是需要对对象监控每一个运行状态，包括引用，赋值等。在 Full GC 的时候，会暂停其他操作，独占 CPU。所以，我们需要控制对象的创建数量，也不要轻易的让对象消失，让他的复用更加充分。

### 对象池
对象池其实就是一个集合，里面包含了我们需要的对象集合，当然这些对象都被池化了，也就是被对象池所管理，想要这样的对象，从池子里取个就行，但是用完得归还。对象池的对象最好是创建比较费时的大对象，如果是太简单的对象，再进入池化的时间比自己构建还多，就不划算了。可以理解对象池为单例模式的延展，多例模式，就那么几个对象实例，再多没有了。

书上说这个模式会用在数据库连接的管理上。比如，每个用户的连接数是有限的，这样每个连接就是一个池子里的一个对象，“连接池”类就可以控制连接数了。

### 自定义一个低质量的对象池
首先构造一个池化对象，也就是对实际对象封装下,为什么呢？当然是为了让对象池更好的管理

```
public  class PooledObject<T> {

 private T objection = null;// 外界使用的对象
 private boolean busy = false; // 此对象是否正在使用的标志，默认没有正在使用

 // 构造函数，池化对象
 public PooledObject(T objection) {
  this.objection = objection;
 }

 // 返回此对象中的对象
 public T getObject() {
  return objection;
 }

 // 设置此对象的，对象
 public void setObject(T objection) {
  this.objection = objection;
 }

 // 获得对象对象是否忙
 public boolean isBusy() {
  return busy;
 }

 // 设置对象的对象正在忙
 public void setBusy(boolean busy) {
  this.busy = busy;
 }
 
 
}
```

池化对象现在包括两个属性，一个是原始对象的引用，另外一个表示当前对象是否在使用

接下来把对象池写出来

```
import java.util.Enumeration;
import java.util.Vector;

public abstract class ObjectPool<T> {
    public static int numObjects = 10; // 对象池的大小
    public static int maxObjects = 50; // 对象池最大的大小
    protected Vector<PooledObject<T>> objects = null; // 存放对象池中对象的向量(PooledObject类型)

    public ObjectPool() {
    }

    /*** 创建一个对象池 ***/
    public synchronized void createPool() {
        // 确保对象池没有创建。如果创建了，保存对象的向量 objects 不会为空
        if (objects != null) {
            return; // 如果己经创建，则返回
        }
        // 创建保存对象的向量 , 初始时有 0 个元素
         objects = new Vector<PooledObject<T>>();
         for (int i = 0; i < numObjects; i++) {
                objects.addElement(create());
            }
    }

    public abstract PooledObject<T> create();

    public synchronized T getObject() {
        // 确保对象池己被创建
        if (objects == null) {
            return null; // 对象池还没创建，则返回 null
        }
        T t = getFreeObject(); // 获得一个可用的对象
        // 如果目前没有可以使用的对象，即所有的对象都在使用中
        while (t == null) {
            wait(250);
            t = getFreeObject(); // 重新再试，直到获得可用的对象，如果
            // getFreeObject() 返回的为 null，则表明创建一批对象后也不可获得可用对象
        }
        return t;// 返回获得的可用的对象
    }

    /**
     * 本函数从对象池对象 objects 中返回一个可用的的对象，如果 当前没有可用的对象，则创建几个对象，并放入对象池中。
     * 如果创建后，所有的对象都在使用中，则返回 null
     */
    private T getFreeObject() {
        // 从对象池中获得一个可用的对象
        T obj = findFreeObject();
        if (obj == null) {
            createObjects(10); // 如果目前对象池中没有可用的对象，创建一些对象
            // 重新从池中查找是否有可用对象
            obj = findFreeObject();
            // 如果创建对象后仍获得不到可用的对象，则返回 null
            if (obj == null) {
                return null;
            }
        }
        return obj;
    }

    public void createObjects(int increment){
        for (int i = 0; i < increment; i++) {
            if (objects.size() > maxObjects) {
                return;
            }
            objects.addElement(create());
        }
    }

    /**
     * 查找对象池中所有的对象，查找一个可用的对象， 如果没有可用的对象，返回 null
     */
    private T findFreeObject() {
        T obj = null;
        PooledObject<T> pObj = null;
        // 获得对象池向量中所有的对象
        Enumeration<PooledObject<T>> enumerate = objects.elements();
        // 遍历所有的对象，看是否有可用的对象
        while (enumerate.hasMoreElements()) {
            pObj = (PooledObject<T>) enumerate.nextElement();

            // 如果此对象不忙，则获得它的对象并把它设为忙
            if (!pObj.isBusy()) {
                obj = pObj.getObject();
                pObj.setBusy(true);
            }
        }
        return obj;// 返回找到到的可用对象
    }

    /**
     * 此函数返回一个对象到对象池中，并把此对象置为空闲。 所有使用对象池获得的对象均应在不使用此对象时返回它。
     */

    public void returnObject(T obj) {
        // 确保对象池存在，如果对象没有创建（不存在），直接返回
        if (objects == null) {
            return;
        }
        PooledObject<T> pObj = null;
        Enumeration<PooledObject<T>> enumerate = objects.elements();
        // 遍历对象池中的所有对象，找到这个要返回的对象对象
        while (enumerate.hasMoreElements()) {
            pObj = (PooledObject<T>) enumerate.nextElement();
            // 先找到对象池中的要返回的对象对象
            if (obj == pObj.getObject()) {
                // 找到了 , 设置此对象为空闲状态
                pObj.setBusy(false);
                break;
            }
        }
    }

    /**
     * 关闭对象池中所有的对象，并清空对象池。
     */
    public synchronized void closeObjectPool() {
        // 确保对象池存在，如果不存在，返回
        if (objects == null) {
            return;
        }
        PooledObject<T> pObj = null;
        Enumeration<PooledObject<T>> enumerate = objects.elements();
        while (enumerate.hasMoreElements()) {
            pObj = (PooledObject<T>) enumerate.nextElement();
            // 如果忙，等 0.5 秒
            if (pObj.isBusy()) {
                wait(500); // 等
            }
            // 从对象池向量中删除它
            objects.removeElement(pObj);
        }
        // 置对象池为空
        objects = null;
    }

    /**
     * 使程序等待给定的毫秒数
     */
    private void wait(int mSeconds) {
        try {
            Thread.sleep(mSeconds);
        } catch (InterruptedException e) {
        }
    }
}

为了泛化处理，这个对象池是个抽象类，接下来具体实现一个
public class DefaultObjectPool extends ObjectPool<String> {

    @Override
    public PooledObject<String> create(){
        return new PooledObject<String>(new String(""+1));
    }

}

```

最后测试下：

```
    public static void main(String[] args) {
        ObjectPool<String> objPool = new DefaultObjectPool();
        objPool.createPool();
        String obj = objPool.getObject();
        objPool.returnObject(obj);
        objPool.closeObjectPool();
    }
```

---
## 二面：

```
1、讲讲数据库存储引擎
2、介绍一下索引，索引设置的规则，聚簇索引和非聚簇索引的区别，索引的最左前缀原则
3、用过redis吗，redis支持哪些数据类型，redis与mysql的区别
4、了解垃圾收集器吗，分别介绍介绍
5、jvm调优做过没，-Xms和-Xmx分别指什么
6、算法题：输入两个字符串，输出它们合并排序后的结果
```

## 三面：

```
1、讲讲数据库的范式
2、Linux进程通信和线程通信
3、线程池的参数
4、什么是内部类，什么是匿名内部类
5、设计题：一个市有9个消防站，现在要新增3个消防站，这3个消防站应该放在哪里
```

---
### Java线程池实例解析

[Java线程池实例解析](https://blog.csdn.net/qq_33142257/article/details/75968791)

#### 线程池概念：

首先，要理解什么是线程池；还是先拿具体例子来说，这个例子可能比较偏了，因为现实生活中接触的少了，但是比较能形象的阐述线程池的含义；


>摆渡，在科技不发达的时候，没有能力造那种大桥，人们过河只能通过码头的船夫摆渡到对岸；为了更贴切，我说的这个码头就只做摆渡的生意，其他运输之类的不干；码头的船则是码头的老板创业时自己造的；

>理解上述的背景后，这样我们就可以拿两者来开始类比了；码头就相当于线程池，而码头里的船就是每一条线程；为什么这样说呢，船其实是一个载体，需要执行的任务其实是过河这个动作，线程也是这样，它也是个载体，完成具体的方法是任务；首先假想一个场景，一个书生到了河边要到对岸去（ps：不管他会不会游泳，还是会飞；这里限定他只能通过船这个途径）；那他只有两种选择，第一个是自己造船，第二个是坐码头里摆渡的船；这两种方式不管是哪个都是完成过河的任务，但是你会发现，码头的船很多，当书生坐上船走后，后面又来了几个人也要过河，他们能立马就载上这几个人；

>码头是船的集合，那么线程池就是线程的集合；为什么要弄出个线程池出来呢，差异就显而易见了，码头的船在客户来之前就已经造好放在那儿等待使用，而如果没有码头的话，客户来到这儿要过河只能自己造船，然后才能过河；这是其一，一个要建造船，一个不要，线程池也是这样，如果没有线程池的话，那么某个方法中要使用多线程的时候，需要new一个线程，而线程池则不一样了，当需要使用线程的时候直接去线程池中取一个，省去了创建线程这个环节，提高效率；

>还有就是多个人需要过河，码头有很多船则会大大的提高效率，不仅省去了造船的环节还省去了资源的占用，如果是每个人自己造船的话，岸边的树估计要被砍光了；多个任务需要执行的时候，手动创建线程会占用太多内存资源，而且每个都需要new，浪费了时间；


好了，其实上述的例子已经把线程池以及它的优点都基本表达出来了，但是还是要结合代码具体讲解下，才能更清楚理解；

#### 线程池

创建一个线程集合，池子的大小取决于内存数量；当需要执行一个任务的时候是重用一个等待的线程，而不是重新开启一个线程；当任务执行完成后继续回到池子中等待下一个任务
优点：

```
1、减少在创建和销毁线程上所花的时间和资源开销
2、与主线程隔离，实现异步执行
```
注意：池中的线程数不是越多越好，线程休眠同样也会占用资源，所以要合理的选择线程池大小

#### 线程池的几个实例
基础类：

```
import java.text.SimpleDateFormat;
import java.util.Date;
 
/**
 * Created by zelei.fan on 2017/6/13.
 */
public class ThreadHandle implements Runnable{
 
    private String index;
 
    ThreadHandle(String index){
        this.index = index;
    }
 
    @Override
    public void run() {
        SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        Date date = new Date();
        String time = df.format(date);
        System.out.println(Thread.currentThread().getName()+"Start Time"+time);
        /*中间一段处理时间*/
        try {
            Thread.sleep(4000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        Date date1 = new Date();
        String time1 = df.format(date1);
        System.out.println(Thread.currentThread().getName()+"End Time"+time1);
    }
}

```

* 1、Executors.newCachedThreadPool()；可缓存的线程池，当线程越多线程池规模也随之扩大,默认超时时间是60s，如果超过会自动终止该线程

```
public static void main(String[] args) {
        /*newCachedThreadPool可缓存线程池同时启动了5个线程来执行*/
        ExecutorService pool = Executors.newCachedThreadPool();
        for (int i = 0; i < 5; i ++){
            pool.execute(new ThreadHandle(String.valueOf(i)));
        }
    }

```

运行结果：

```
pool-1-thread-3Start Time2017-07-24 13:35:28
pool-1-thread-1Start Time2017-07-24 13:35:28
pool-1-thread-5Start Time2017-07-24 13:35:28
pool-1-thread-4Start Time2017-07-24 13:35:28
pool-1-thread-2Start Time2017-07-24 13:35:28
pool-1-thread-2End Time2017-07-24 13:35:32
pool-1-thread-3End Time2017-07-24 13:35:32
pool-1-thread-4End Time2017-07-24 13:35:32
pool-1-thread-1End Time2017-07-24 13:35:32
pool-1-thread-5End Time2017-07-24 13:35:32
```

可以看到线程池同时启动了5个线程来执行任务，理论上可开启的线程数是无限的，但是受cpu的影响，cpu越多的话开的越多；

* 2、Executors.newFixedThreadPool(2)；创建一个固定大小的线程池；当线程达到最大数时，大小不再变化，适用于固定的稳定的并发编程

```
public static void main(String[] args) {
        /*newFixedThreadPool固定线程数的线程池同一时刻最多只开启设定的5个线程，只有当前面的线程结束后才会继续执行后面等待的任务*/
        ExecutorService executorService = Executors.newFixedThreadPool(2);
        for (int i = 0; i < 6; i ++){
            executorService.execute(new ThreadHandle(String.valueOf(i)));
        }
    }

```

运行结果：

```
pool-1-thread-2Start Time2017-07-24 13:42:04
pool-1-thread-1Start Time2017-07-24 13:42:04
pool-1-thread-1End Time2017-07-24 13:42:08
pool-1-thread-2End Time2017-07-24 13:42:08
pool-1-thread-1Start Time2017-07-24 13:42:08
pool-1-thread-2Start Time2017-07-24 13:42:08
pool-1-thread-1End Time2017-07-24 13:42:12
pool-1-thread-2End Time2017-07-24 13:42:12
pool-1-thread-1Start Time2017-07-24 13:42:12
pool-1-thread-2Start Time2017-07-24 13:42:12
pool-1-thread-2End Time2017-07-24 13:42:16
pool-1-thread-1End Time2017-07-24 13:42:16
```

同时开启是个任务来执行，但是线程池大小只有2，一个时间点只能有两个线程并行，其余的任务则必须等待，直到上一个任务执行完成；

* 3、Executors.newSingleThreadExecutor()；创建一个单线程，串行执行

```
public static void main(String[] args) {
        /*newSingleThreadExecutor单线程,同一时刻只有一个线程实例，所有任务都用这个实例执行
        * 相当于Executors.newFixedThreadPool(1)；
        * */
        ExecutorService singleThreadExecutor = Executors.newSingleThreadExecutor();
        for (int i = 0; i < 5; i ++){
            singleThreadExecutor.execute(new ThreadHandle(String.valueOf(i)));
        }
    }

```

运行结果：

```
pool-1-thread-1Start Time2017-07-24 13:46:07
pool-1-thread-1End Time2017-07-24 13:46:11
pool-1-thread-1Start Time2017-07-24 13:46:11
pool-1-thread-1End Time2017-07-24 13:46:15
pool-1-thread-1Start Time2017-07-24 13:46:15
pool-1-thread-1End Time2017-07-24 13:46:19
pool-1-thread-1Start Time2017-07-24 13:46:19
pool-1-thread-1End Time2017-07-24 13:46:23
pool-1-thread-1Start Time2017-07-24 13:46:23
pool-1-thread-1End Time2017-07-24 13:46:27
```

上述结果中至始至终就只有一个线程thread-1在运行；

* 4、Executors.newScheduledThreadPool(5)；计划类线程池

1）、延时执行：

```
public static void main(String[] args) {
        /*计划类线程池*/
        ScheduledExecutorService scheduledExecutorService = Executors.newScheduledThreadPool(5);
        for (int i = 0; i < 5; i ++){
            /*延时10秒执行*/
            scheduledExecutorService.schedule(new ThreadHandle(String.valueOf(i)), 10, TimeUnit.SECONDS);
        }
        SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        Date date = new Date();
        String time = df.format(date);
        System.out.println(time);
    }

```

打印结果：

```
2017-07-24 13:54:10
pool-1-thread-2Start Time2017-07-24 13:54:20
pool-1-thread-3Start Time2017-07-24 13:54:20
pool-1-thread-1Start Time2017-07-24 13:54:20
pool-1-thread-4Start Time2017-07-24 13:54:20
pool-1-thread-5Start Time2017-07-24 13:54:20
pool-1-thread-3End Time2017-07-24 13:54:24
pool-1-thread-2End Time2017-07-24 13:54:24
pool-1-thread-4End Time2017-07-24 13:54:24
pool-1-thread-1End Time2017-07-24 13:54:24
pool-1-thread-5End Time2017-07-24 13:54:24
```

可以看到线程在十秒之后才开始执行；

2）、间断执行：

```
public static void main(String[] args) {
        /*计划类线程池*/
        ScheduledExecutorService scheduledExecutorService = Executors.newScheduledThreadPool(5);
        for (int i = 0; i < 5; i ++){
            /*从0ms开始每隔10秒执行一个任务，需要注意的是这个时间间隔是从上一个线程开始时计算的*/
            scheduledExecutorService.scheduleAtFixedRate(new ThreadHandle(String.valueOf(i)), 0, 10, TimeUnit.SECONDS);
        }
    }

```

打印结果：

```
pool-1-thread-1Start Time2017-07-24 13:59:17
pool-1-thread-5Start Time2017-07-24 13:59:17
pool-1-thread-2Start Time2017-07-24 13:59:17
pool-1-thread-4Start Time2017-07-24 13:59:17
pool-1-thread-3Start Time2017-07-24 13:59:17
pool-1-thread-1End Time2017-07-24 13:59:21
pool-1-thread-4End Time2017-07-24 13:59:21
pool-1-thread-5End Time2017-07-24 13:59:21
pool-1-thread-3End Time2017-07-24 13:59:21
pool-1-thread-2End Time2017-07-24 13:59:21
pool-1-thread-4Start Time2017-07-24 13:59:27
pool-1-thread-1Start Time2017-07-24 13:59:27
pool-1-thread-5Start Time2017-07-24 13:59:27
pool-1-thread-3Start Time2017-07-24 13:59:27
pool-1-thread-2Start Time2017-07-24 13:59:27
```

线程每次开始于上一次开始间隔都是10s，而且会无限的执行下去，类似于定时任务；

3）、间断执行:

```
public static void main(String[] args) {
        /*计划类线程池*/
        ScheduledExecutorService scheduledExecutorService = Executors.newScheduledThreadPool(5);
        for (int i = 0; i < 5; i ++){
            /*从0ms开始每隔10秒执行一个任务，需要注意的是这个时间间隔是从上一个线程结束后计算的*/
            scheduledExecutorService.scheduleWithFixedDelay(new ThreadHandle(String.valueOf(i)), 0, 10, TimeUnit.SECONDS);
        }
    }

```

打印结果：

```
pool-1-thread-5Start Time2017-07-24 14:02:49
pool-1-thread-3Start Time2017-07-24 14:02:49
pool-1-thread-2Start Time2017-07-24 14:02:49
pool-1-thread-4Start Time2017-07-24 14:02:49
pool-1-thread-1Start Time2017-07-24 14:02:49
pool-1-thread-3End Time2017-07-24 14:02:53
pool-1-thread-2End Time2017-07-24 14:02:53
pool-1-thread-5End Time2017-07-24 14:02:53
pool-1-thread-4End Time2017-07-24 14:02:53
pool-1-thread-1End Time2017-07-24 14:02:53
pool-1-thread-3Start Time2017-07-24 14:03:03
pool-1-thread-5Start Time2017-07-24 14:03:03
pool-1-thread-4Start Time2017-07-24 14:03:03
pool-1-thread-1Start Time2017-07-24 14:03:03
pool-1-thread-2Start Time2017-07-24 14:03:03
```

上面结果是每次线程和上一次线程执行结束后间隔10s；

#### 多线程回调：
基础类：

```
import java.util.concurrent.Callable;
 
/**
 * Created by zelei.fan on 2017/6/13.
 */
public class CallableTest implements Callable<String> {
 
    private int index;
 
    public CallableTest(int index){
        this.index = index;
    }
 
    @Override
    public String call() throws Exception {
        System.out.println("call()方法被自动调用, 开始*******" + Thread.currentThread().getName());
        if(index > 5){
            Thread.sleep(1000);
        }else {
            Thread.sleep(10000);
        }
        return "call()方法被自动调用，结束*******" + Thread.currentThread().getName();
    }
}

```

submit：将线程放入线程池中，除了使用execute，还可以使用submit,而且能够获取回调，submit适用于生产者-消费者模式，和Future一起使用起到没有返回结果就阻塞当前线程，等待线程池返回结果；

```
public static void main(String[] args) {
        ExecutorService pool1 = Executors.newCachedThreadPool();
        List<Future<String>> futureList = Lists.newArrayList();
        for (int i = 0; i < 5; i ++){
            Future<String> future = pool1.submit(new CallableTest(i));
            futureList.add(future);
        }
        futureList.forEach(new Consumer<Future<String>>() {
            @Override
            public void accept(Future<String> stringFuture) {
                try {
                    System.out.println(stringFuture.get());
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (ExecutionException e) {
                    e.printStackTrace();
                }
            }
        });
    }

```

打印结果：

```
call()方法被自动调用, 开始*******pool-1-thread-1
call()方法被自动调用, 开始*******pool-1-thread-4
call()方法被自动调用, 开始*******pool-1-thread-5
call()方法被自动调用, 开始*******pool-1-thread-3
call()方法被自动调用, 开始*******pool-1-thread-2
call()方法被自动调用，结束*******pool-1-thread-1
call()方法被自动调用，结束*******pool-1-thread-2
call()方法被自动调用，结束*******pool-1-thread-3
call()方法被自动调用，结束*******pool-1-thread-4
call()方法被自动调用，结束*******pool-1-thread-5
```

调用结束打印的语句都是在run方法中返回出来的，通过future来接受返回参数，然后将future打印；

说到回调，还有一种就是CompletionService，区别是future是阻塞的，而CompletionService是异步非阻塞的；
看下具体例子：

```
public static void main(String[] args) {
        ExecutorService pool1 = Executors.newCachedThreadPool();
        CompletionService service = new ExecutorCompletionService(pool1);
        for (int i = 0; i < 10; i ++){
            service.submit(new CallableTest(i));
        }
        for(int i = 0; i < 10; i ++){
            try {
                SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
                Date date = new Date();
                String time = df.format(date);
                System.out.println(service.take().get() + time);
            } catch (InterruptedException e) {
                e.printStackTrace();
            } catch (ExecutionException e) {
                e.printStackTrace();
            }
        }
    }

```

打印结果：

```
call()方法被自动调用, 开始*******pool-1-thread-1
call()方法被自动调用, 开始*******pool-1-thread-5
call()方法被自动调用, 开始*******pool-1-thread-6
call()方法被自动调用, 开始*******pool-1-thread-2
call()方法被自动调用, 开始*******pool-1-thread-9
call()方法被自动调用, 开始*******pool-1-thread-10
call()方法被自动调用, 开始*******pool-1-thread-3
call()方法被自动调用, 开始*******pool-1-thread-7
call()方法被自动调用, 开始*******pool-1-thread-4
call()方法被自动调用, 开始*******pool-1-thread-8
call()方法被自动调用，结束*******pool-1-thread-72017-07-24 15:10:00
call()方法被自动调用，结束*******pool-1-thread-102017-07-24 15:10:01
call()方法被自动调用，结束*******pool-1-thread-92017-07-24 15:10:01
call()方法被自动调用，结束*******pool-1-thread-82017-07-24 15:10:01
call()方法被自动调用，结束*******pool-1-thread-62017-07-24 15:10:01
call()方法被自动调用，结束*******pool-1-thread-52017-07-24 15:10:10
call()方法被自动调用，结束*******pool-1-thread-22017-07-24 15:10:10
call()方法被自动调用，结束*******pool-1-thread-12017-07-24 15:10:10
call()方法被自动调用，结束*******pool-1-thread-32017-07-24 15:10:10
call()方法被自动调用，结束*******pool-1-thread-42017-07-24 15:10:10
```

通过上面一个例子可以知道Future是阻塞取出结果的，按顺序执行，如果说正常的前面的线程执行完成后面的线程还在执行中的话，前面线程的结果时可以直接返回的，但是如果后面的线程比前面的线程先执行完成，则后面线程的返回结果需要等待前面线程返回后才能取得结果； 而CompletionService是异步非阻塞的，哪个执行完成有回调了，哪个就能输出结果；

---

# 北京 快手 大数据研发技术面经

作者：LMagic
链接：https://www.nowcoder.com/discuss/148752?type=2&order=0&pos=92&page=1
来源：牛客网

## Java

```
1. HashMap的底层数据结构, 为什么JDK8要用红黑树. ConcurrentHashMap的底层数据结构, 如何保证线程安全
2. synchronized关键字的本质, 作用是什么. volatile关键字的作用, 哪些情况下会用它
3. Java线程的几大状态及转换. 线程可重入是什么概念, 可重入锁呢
4. 如果要设计一个线程池, 需要考虑哪些要素. Executors工厂类能创建哪些线程池, 用过哪些
5. 讲一讲熟悉的设计模式. 单例模式及工厂模式的实现方法. 装饰器模式是怎么一回事
6. 讲一讲熟悉的JVM GC算法, 常用的垃圾收集器. CMS有什么优缺点
7. 一个Java应用上线后, 关注哪些性能指标. 如果响应时间过长或者CPU占用过高, 如何排查, 用哪些工具或命令
```

### HashMap的底层数据结构

[hashMap的底层数据结构：数组+链表](https://blog.csdn.net/anlve512/article/details/77035334)

HashMap也是我们使用非常多的Collection，它是基于哈希表的 Map 接口的实现，以key-value的形式存在。在HashMap中，key-value总是会当做一个整体来处理，系统会根据hash算法来来计算key-value的存储位置，我们总是可以通过key快速地存、取value。

数据结构中有数组和链表来实现对数据的存储，但这两者基本上是两个极端。

* 数组
数组存储区间连续，占用内存比较严重，空间复杂度很大。但数组的二分查找时间复杂度小，为O(1)；

数组的特点是：寻址容易，插入和删除困难；

* 链表

链表存储区间离散，占用内存比较宽松，空间复杂度很小，但时间复杂度很大，达O（N）。

链表的特点是：寻址困难，插入和删除容易。

> 实际上hashMap是一个链表散列

> 哈希表是由数组+链表组成的，

HashMap其实也是一个线性的数组实现的,所以可以理解为其存储数据的容器就是一个线性数组。这可能让我们很不解，一个线性的数组怎么实现按键值对来存取数据呢？这里HashMap有做一些处理。

首先HashMap里面实现一个静态内部类Entry，其重要的属性有 key , value, next，从属性key,value我们就能很明显的看出来Entry就是HashMap键值对实现的一个基础bean，我们上面说到HashMap的基础就是一个线性数组，这个数组就是Entry[]，Map里面的内容都保存在Entry[]里面。

---

[hashmap的实现原理 数组 entry](https://blog.csdn.net/strivenoend/article/details/80397825)

存储结构
hashmap底层是以数组方式进行存储。将key-value对作为数组中的一个元素进行存储。

key-value都是Map.Entry中的属性。

其中将key的值进行hash之后进行存储，即每一个key都是计算hash值，然后再存储。每一个Hash值对应一个数组下标，数组下标是根据hash值和数组长度计算得来。

由于不同的key有可能hash值相同，即该位置的数组中的元素出现两个，对于这种情况，hashmap采用链表形式进行存储。
下图描述了hashmap的存储结构图 
hashmap结构

![](https://img-blog.csdn.net/20171127105027500?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvanVuY2hlbmJiMDQzMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 为什么JDK8要用红黑树

[HashMap 在 JDK 1.8 后新增的红黑树结构](https://blog.csdn.net/wushiwude/article/details/75331926)



* 传统 HashMap 的缺点

JDK 1.8 以前 HashMap 的实现是 数组+链表，即使哈希函数取得再好，也很难达到元素百分百均匀分布。



当 HashMap 中有大量的元素都存放到同一个桶中时，这个桶下有一条长长的链表，这个时候 HashMap 就相当于一个单链表，假如单链表有 n 个元素，遍历的时间复杂度就是 O(n)，完全失去了它的优势。



针对这种情况，JDK 1.8 中引入了 红黑树（查找时间复杂度为 O(logn)）来优化这个问题。



* HashMap 在 JDK 1.8 中新增的数据结构 – 红黑树

  

![](https://img-blog.csdn.net/20161126224228580)

#### 重温数据结构：深入理解红黑树

[重温数据结构：深入理解红黑树](https://blog.csdn.net/u011240877/article/details/53329023)

 二叉排序树的性能取决于二叉树的层数：

* 最好的情况是 O(logn)，存在于完全二叉排序树情况下，其访问性能近似于折半查找；
* 最差时候会是 O(n)，比如插入的元素是有序的，生成的二叉排序树就是一个链表，这种情况下，需要遍历全部元素才行（见下图 b）。

![](https://img-blog.csdn.net/20161120170528505)

为了改变排序二叉树存在的不足，Rudolf Bayer 在 1972 年发明了另一种改进后的排序二叉树：红黑树，他将这种排序二叉树称为“对称二叉 B 树”，而红黑树这个名字则由 Leo J. Guibas 和 Robert Sedgewick 于 1978 年首次提出。



* 什么是红黑树

红黑树本质上是一种二叉查找树，但它在二叉查找树的基础上额外添加了一个标记（颜色），同时具有一定的规则。这些规则使红黑树保证了一种平衡，插入、删除、查找的最坏时间复杂度都为 O(logn)。

它的统计性能要好于平衡二叉树（AVL树），因此，红黑树在很多地方都有应用。比如在 Java 集合框架中，很多部分(HashMap, TreeMap, TreeSet 等)都有红黑树的应用，这些集合均提供了很好的性能。

由于 TreeMap 就是由红黑树实现的，因此本文将使用 TreeMap 的相关操作的代码进行分析、论证。

* 黑色高度

从根节点到叶节点的路径上黑色节点的个数，叫做树的黑色高度。

* 红黑树的 5 个特性

![](https://img-blog.csdn.net/20161123195416588)

红黑树在原有的二叉查找树基础上增加了如下几个要求：



```
1. 每个节点要么是红色，要么是黑色；
2. 根节点永远是黑色的；
3. 所有的叶节点都是是黑色的（注意这里说叶子节点其实是上图中的 NIL 节点）；
4. 每个红色节点的两个子节点一定都是黑色；
5. 从任一节点到其子树中每个叶子节点的路径都包含相同数量的黑色节点；

```

注意： 

```
性质 3 中指定红黑树的每个叶子节点都是空节点，而且并叶子节点都是黑色。但 Java 实现的红黑树将使用 null 来代表空节点，因此遍历红黑树时将看不到黑色的叶子节点，反而看到每个叶子节点都是红色的。

性质 4 的意思是：从每个根到节点的路径上不会有两个连续的红色节点，但黑色节点是可以连续的。 

因此若给定黑色节点的个数 N，最短路径的情况是连续的 N 个黑色，树的高度为 N - 1;最长路径的情况为节点红黑相间，树的高度为 2(N - 1) 。

性质 5 是成为红黑树最主要的条件，后序的插入、删除操作都是为了遵守这个规定。

```

红黑树并不是标准平衡二叉树，它以性质 5 作为一种平衡方法，使自己的性能得到了提升.



* 红黑树的左旋右旋



![shixinzhang](https://img-blog.csdn.net/20161122151257178)

红黑树的左右旋是比较重要的操作，左右旋的目的是调整红黑节点结构，转移黑色节点位置，使其在进行插入、删除后仍能保持红黑树的 5 条性质。

比如 X 左旋(右图转成左图)的结果，是让在 Y 左子树的黑色节点跑到 X 右子树去。



* 红黑树的平衡插入

红黑树的插入主要分两步：



```
首先和二叉查找树的插入一样，查找、插入

然后调整结构，保证满足红黑树状态 

- 对结点进行重新着色
- 以及对树进行相关的旋转操作

```

红黑树的插入在二叉查找树插入的基础上，为了重新恢复平衡，继续做了插入修复操作。



*  总结



红黑树并不是真正的平衡二叉树，但在实际应用中，红黑树的统计性能要高于平衡二叉树，但极端性能略差。

红黑树的插入、删除调整逻辑比较复杂，但最终目的是满足红黑树的 5 个特性，尤其是 4 和 5。

在插入调整时为了简化操作我们直接把插入的节点涂成红色，这样只要保证插入节点的父节点不是红色就可以了。

而在删除后的调整中，针对删除黑色节点，所在子树缺少一个节点，需要进行弥补或者对别人造成一个黑色节点的伤害。具体调整方法取决于兄弟节点所在子树的情况。

红黑树的插入、删除在树形数据结构中算比较复杂的，理解起来比较难，但只要记住，红黑树有其特殊的平衡规则，而我们为了维持平衡，根据邻树的状况进行旋转或者涂色。

红黑树这么难理解，必定有其过人之处。它的有序、快速特性在很多场景下都有用到，比如 Java 集合框架的 TreeMap, TreeSet 等。



---



JDK 1.8 中 HashMap 中除了链表节点：



```
static class Node<K,V> implements Map.Entry<K,V> {
    //哈希值，就是位置
    final int hash;
    //键
    final K key;
    //值
    V value;
    //指向下一个几点的指针
    Node<K,V> next;
    //...
}
```



还有另外一种节点：TreeNode，它是 1.8 新增的，属于[数据结构](http://lib.csdn.net/base/datastructure)中的 红黑树



```
static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
    TreeNode<K,V> parent;  // red-black tree links
    TreeNode<K,V> left;
    TreeNode<K,V> right;
    TreeNode<K,V> prev;    // needed to unlink next upon deletion
    boolean red;
}
```



可以看到就是个红黑树节点，有父亲、左右孩子、前一个元素的节点，还有个颜色值。

另外由于它继承自 LinkedHashMap.Entry ，而 LinkedHashMap.Entry 继承自 HashMap.Node ，因此还有额外的 6 个属性：

```
//继承 LinkedHashMap.Entry 的
Entry<K,V> before, after;
 
//HashMap.Node 的
final int hash;
final K key;
V value;
Node<K,V> next;
```



> java8不是用红黑树来管理hashmap，而是在hash值相同的情况下（且重复数量大于8），用红黑树来管理数据。 红黑树相当于排序数据。可以自动的使用二分法进行定位。性能较高。 一般情况下，hash值做的比较好的话基本上用不到红黑树。



---



### ConcurrentHashMap的底层数据结构, 如何保证线程安全

[Java容器（二）-CurrentHashMap详解（JDK1.8）](https://blog.csdn.net/programerxiaoer/article/details/80040090)

* 摘要

在涉及到Java多线程开发时，如果我们使用HashMap可能会导致死锁问题，使用HashTable效率又不高。但是ConcurrentHashMap既可以保持同步也可以提高并发效率，所以这个时候ConcurrentHashmap是我们最好的选择。 

* 为什么使用ConcurrentHashMap：

在多线程环境中使用HashMap的put方法有可能导致程序死循环，因为多线程可能会导致HashMap形成环形链表，即链表的一个节点的next节点永不为null，就会产生死循环。这时，CPU的利用率接近100%，所以并发情况下不能使用HashMap。

HashTable通过使用synchronized保证线程安全，但在线程竞争激烈的情况下效率低下。因为当一个线程访问HashTable的同步方法时，其他线程只能阻塞等待占用线程操作完毕。

ConcurrentHashMap使用分段锁的思想，对于不同的数据段使用不同的锁，可以支持多个线程同时访问不同的数据段，这样线程之间就不存在锁竞争，从而提高了并发效率。

* Java7与Java8中的ConcurrentHashMap： 

在ConcurrentHashMap中主要通过锁分段技术实现上述目标。 

> 在Java7中，ConcurrentHashMap由Segment数组结构和HashEntry数组组成。Segment是一种可重入锁，是一种数组和链表的结构，一个Segment中包含一个HashEntry数组，每个HashEntry又是一个链表结构。正是通过Segment分段锁，ConcurrentHashMap实现了高效率的并发。 



> 在Java8中，ConcurrentHashMap去除了Segment分段锁的数据结构，主要是基于CAS操作保证保证数据的获取以及使用synchronized关键字对相应数据段加锁实现了主要功能，这进一步提高了并发性。同时同时为了提高哈希碰撞下的寻址性能，Java 8在链表长度超过一定阈值(8)时将链表（寻址时间复杂度为O(N)）转换为红黑树（寻址时间复杂度为O(long(N))).



* Java8中ConcurrentHashMap的结构

在Java8中，ConcurrentHashMap弃用了Segment类，但是保留了Segment属性，用于序列化。目前ConcurrentHashMap采用Node类作为基本的存储单元，每个键值对(key-value)都存储在一个Node中。同时Node也有一些子类，TreeNodes用于树结构中（当链表长度大于8时转化为红黑树）；TreeBins用于维护TreeNodes。当链表转树时，用于封装TreeNode。也就是说，ConcurrentHashMap的红黑树存放的是TreeBin，而不是treeNode；ForwordingNodes是一个重要的结构，它用于ConcurrentHashMap扩容时，是一个标志节点，内部有一个指向nextTable的属性，同时也提供了查找的方法；



* ConcurrentHashMap的put方法

```
- 如果key或者value为null，则抛出空指针异常； 

- 如果table为null或者table的长度为0，则初始化table，调用initTable()方法。 

- 计算当前键值的索引位置，如果Hash表中当前节点为null，则将元素直接插入。(注意，这里使用的就是前面锁说的CAS操作) 

- 如果当前位置的节点元素的hash值为-1，说明这是一个ForwaringNodes节点，即正在进行扩容。那么当前线程加入扩容。 

- 当前节点不为null，对当前节点加锁，将元素插入到当前节点。在Java8中，当节点长度大于8时，就将节点转为树的结构。

```



* ConcurrentHashMap的get方法 

```
- ConcurrentHashMap的get方法就是从Hash表中读取数据，而且与扩容不冲突。该方法没有同步锁。 

- 通过键值的hash计算索引位置，如果满足条件，直接返回对应的值； 

- 如果相应节点的hash值小于0 ，即该节点在进行扩容，直接在调用ForwardingNodes节点的find方法进行查找。 

- 否则，遍历当前节点直到找到对应的元素。

```



### synchronized关键字的本质, 作用是什么. 

[java中synchronized关键字的用法](http://www.cnblogs.com/wl0000-03/p/5973039.html)



* java的内置锁：

每个java对象都可以用做一个实现同步的锁，这些锁成为内置锁。线程进入同步代码块或方法的时候会自动获得该锁，在退出同步代码块或方法时会释放该锁。获得内置锁的唯一途径就是进入这个锁的保护的同步代码块或方法。    



 java内置锁是一个互斥锁，这就是意味着最多只有一个线程能够获得该锁，当线程A尝试去获得线程B持有的内置锁时，线程A必须等待或者阻塞，知道线程B释放这个锁，如果B线程不释放这个锁，那么A线程将永远等待下去。    



* java的对象锁和类锁：

  

  java的对象锁和类锁在锁的概念上基本上和内置锁是一致的，但是，两个锁实际是有很大的区别的，

  ```
  对象锁是用于对象实例方法，或者一个对象实例上的，
  
  类锁是用于类的静态方法或者一个类的class对象上的。
  
  ```

我们知道，类的对象实例可以有很多个，但是每个类只有一个class对象，所以不同对象实例的对象锁是互不干扰的，但是每个类只有一个类锁。但是有一点必须注意的是，其实类锁只是一个概念上的东西，并不是真实存在的，它只是用来帮助我们理解锁定实例方法和静态方法的区别的



下面分别分析这两种用法在对象锁和类锁上的效果。

对象锁的synchronized修饰方法和代码块：

```
public class TestSynchronized 
{  
    public void test1() 
    {  
         synchronized(this) 
         {  
              int i = 5;  
              while( i-- > 0) 
              {  
                   System.out.println(Thread.currentThread().getName() + " : " + i);  
                   try 
                   {  
                        Thread.sleep(500);  
                   } 
                   catch (InterruptedException ie) 
                   {  
                   }  
              }  
         }  
    }  
    public synchronized void test2() 
    {  
         int i = 5;  
         while( i-- > 0) 
         {  
              System.out.println(Thread.currentThread().getName() + " : " + i);  
              try 
              {  
                   Thread.sleep(500);  
              } 
              catch (InterruptedException ie) 
              {  
              }  
         }  
    }  
    public static void main(String[] args) 
    {  
         final TestSynchronized myt2 = new TestSynchronized();  
         Thread test1 = new Thread(  new Runnable() {  public void run() {  myt2.test1();  }  }, "test1"  );  
         Thread test2 = new Thread(  new Runnable() {  public void run() { myt2.test2();   }  }, "test2"  );  
         test1.start();;  
         test2.start();  
//         TestRunnable tr=new TestRunnable();
//         Thread test3=new Thread(tr);
//         test3.start();
    } 
}
 

 

test2 : 4
test2 : 3
test2 : 2
test2 : 1
test2 : 0
test1 : 4
test1 : 3
test1 : 2
test1 : 1
test1 : 0
```



```
上述的代码，
第一个方法时用了同步代码块的方式进行同步，传入的对象实例是this，表明是当前对象，当然，如果需要同步其他对象实例，也不可传入其他对象的实例；
第二个方法是修饰方法的方式进行同步。因为第一个同步代码块传入的this，所以两个同步代码所需要获得的对象锁都是同一个对象锁，
下面main方法时分别开启两个线程，分别调用test1和test2方法，那么两个线程都需要获得该对象锁，另一个线程必须等待。
上面也给出了运行的结果可以看到：直到test2线程执行完毕，释放掉锁，test1线程才开始执行。

（可能这个结果有人会有疑问，代码里面明明是先开启test1线程，为什么先执行的是test2呢？这是因为java编译器在编译成字节码的时候，会对代码进行一个重排序，也就是说，编译器会根据实际情况对代码进行一个合理的排序，编译前代码写在前面，在编译后的字节码不一定排在前面，所以这种运行结果是正常的， 这里是题外话，最主要是检验synchronized的用法的正确性）
```



类锁的修饰（静态）方法和代码块：

```
public class TestSynchronized 
{  
    public void test1() 
    {  
         synchronized(TestSynchronized.class) 
         {  
              int i = 5;  
              while( i-- > 0) 
              {  
                   System.out.println(Thread.currentThread().getName() + " : " + i);  
                   try 
                   {  
                        Thread.sleep(500);  
                   } 
                   catch (InterruptedException ie) 
                   {  
                   }  
              }  
         }  
    }  
    public static synchronized void test2() 
    {  
         int i = 5;  
         while( i-- > 0) 
         {  
              System.out.println(Thread.currentThread().getName() + " : " + i);  
              try 
              {  
                   Thread.sleep(500);  
              } 
              catch (InterruptedException ie) 
              {  
              }  
         }  
    }  
    public static void main(String[] args) 
    {  
         final TestSynchronized myt2 = new TestSynchronized();  
         Thread test1 = new Thread(  new Runnable() {  public void run() {  myt2.test1();  }  }, "test1"  );  
         Thread test2 = new Thread(  new Runnable() {  public void run() { TestSynchronized.test2();   }  }, "test2"  );  
         test1.start();  
         test2.start();  
//         TestRunnable tr=new TestRunnable();
//         Thread test3=new Thread(tr);
//         test3.start();
    } 
}
 

test1 : 4
test1 : 3
test1 : 2
test1 : 1
test1 : 0
test2 : 4
test2 : 3
test2 : 2
test2 : 1
test2 : 0
```

 


上面代码synchronized同时修饰静态方法和实例方法，但是运行结果是交替进行的，这证明了类锁和对象锁是两个不一样的锁，控制着不同的区域，它们是互不干扰的。同样，线程获得对象锁的同时，也可以获得该类锁，即同时获得两个锁，这是允许的。

到这里，对synchronized的用法已经有了一定的了解。这时有一个疑问，既然有了synchronized修饰方法的同步方式，为什么还需要synchronized修饰同步代码块的方式呢？而这个问题也是synchronized的缺陷所在



* synchronized的缺陷：

当某个线程进入同步方法获得对象锁，那么其他线程访问这里对象的同步方法时，必须等待或者阻塞，这对高并发的系统是致命的，这很容易导致系统的崩溃。如果某个线程在同步方法里面发生了死循环，那么它就永远不会释放这个对象锁，那么其他线程就要永远的等待。这是一个致命的问题。



当然同步方法和同步代码块都会有这样的缺陷，只要用了synchronized关键字就会有这样的风险和缺陷。既然避免不了这种缺陷，那么就应该将风险降到最低。这也是同步代码块在某种情况下要优于同步方法的方面。

例如在某个类的方法里面：这个类里面声明了一个对象实例，SynObject so=new SynObject()；在某个方法里面调用了这个实例的方法so.testsy();但是调用这个方法需要进行同步，不能同时有多个线程同时执行调用这个方法。

这时如果直接用synchronized修饰调用了so.testsy();代码的方法，那么当某个线程进入了这个方法之后，这个对象其他同步方法都不能给其他线程访问了。假如这个方法需要执行的时间很长，那么其他线程会一直阻塞，影响到系统的性能。

如果这时用synchronized来修饰代码块：synchronized（so）{so.testsy();}，那么这个方法加锁的对象是so这个对象，跟执行这行代码的对象没有关系，当一个线程执行这个方法时，这对其他同步方法时没有影响的，因为他们持有的锁都完全不一样。







---

### volatile关键字的作用, 哪些情况下会用它



[volatile关键字及其作用](https://blog.csdn.net/u010255818/article/details/65633033)

#### 1 保证内存可见性

##### 1.1 基本概念

可见性是指线程之间的可见性，一个线程修改的状态对另一个线程是可见的。也就是一个线程修改的结果，另一个线程马上就能看到。

##### 1.2 实现原理

当对非volatile变量进行读写的时候，每个线程先从主内存拷贝变量到CPU缓存中，如果计算机有多个CPU，每个线程可能在不同的CPU上被处理，这意味着每个线程可以拷贝到不同的CPU cache中。 

volatile变量不会被缓存在寄存器或者对其他处理器不可见的地方，保证了每次读写变量都从主内存中读，跳过CPU cache这一步。当一个线程修改了这个变量的值，新值对于其他线程是立即得知的。

![](https://img-blog.csdn.net/20170324153250020?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDI1NTgxOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### 2 适用场景



```
（1）volatile是轻量级同步机制。在访问volatile变量时不会执行加锁操作，因此也就不会使执行线程阻塞，是一种比synchronized关键字更轻量级的同步机制。 

（2）volatile无法同时保证内存可见性和原子性。加锁机制既可以确保可见性又可以确保原子性，而volatile变量只能确保可见性。 

（3）volatile不能修饰写入操作依赖当前值的变量。声明为volatile的简单变量如果当前值与该变量以前的值相关，那么volatile关键字不起作用，也就是说如下的表达式都不是原子操作：“count++”、“count = count+1”。 

（4）当要访问的变量已在synchronized代码块中，或者为常量时，没必要使用volatile； 

（5）volatile屏蔽掉了JVM中必要的代码优化，所以在效率上比较低，因此一定在必要时才使用此关键字。

```



[正确使用 Volatile 变量](https://www.ibm.com/developerworks/cn/java/j-jtp06197.html)

Volatile 变量具有 `synchronized` 的可见性特性，但是不具备原子特性。

这就是说线程能够自动发现 volatile 变量的最新值。Volatile 变量可用于提供线程安全，但是只能应用于非常有限的一组用例：多个变量之间或者某个变量的当前值与修改后值之间没有约束。因此，单独使用 volatile 还不足以实现计数器、互斥锁或任何具有与多个变量相关的不变式（Invariants）的类（例如 “start <=end”）。

出于简易性或可伸缩性的考虑，您可能倾向于使用 volatile 变量而不是锁。当使用 volatile 变量而非锁时，某些习惯用法（idiom）更加易于编码和阅读。此外，volatile 变量不会像锁那样造成线程阻塞，因此也很少造成可伸缩性问题。在某些情况下，如果读操作远远大于写操作，volatile 变量还可以提供优于锁的性能优势。

您只能在有限的一些情形下使用 volatile 变量替代锁。要使 volatile 变量提供理想的线程安全，必须同时满足下面两个条件：

- 对变量的写操作不依赖于当前值。
- 该变量没有包含在具有其他变量的不变式中。

实际上，这些条件表明，可以被写入 volatile 变量的这些有效值独立于任何程序的状态，包括变量的当前状态。

第一个条件的限制使 volatile 变量不能用作线程安全计数器。虽然增量操作（`x++`）看上去类似一个单独操作，实际上它是一个由读取－修改－写入操作序列组成的组合操作，必须以原子方式执行，而 volatile 不能提供必须的原子特性。实现正确的操作需要使 `x` 的值在操作期间保持不变，而 volatile 变量无法实现这点。（然而，如果将值调整为只从单个线程写入，那么可以忽略第一个条件。）

大多数编程情形都会与这两个条件的其中之一冲突，使得 volatile 变量不能像 `synchronized` 那样普遍适用于实现线程安全。清单 1 显示了一个非线程安全的数值范围类。它包含了一个不变式 —— 下界总是小于或等于上界。

清单 1. 非线程安全的数值范围类

```
@NotThreadSafe 
public class NumberRange {
    private int lower, upper;
 
    public int getLower() { return lower; }
    public int getUpper() { return upper; }
 
    public void setLower(int value) { 
        if (value > upper) 
            throw new IllegalArgumentException(...);
        lower = value;
    }
 
    public void setUpper(int value) { 
        if (value < lower) 
            throw new IllegalArgumentException(...);
        upper = value;
    }
}
```

这种方式限制了范围的状态变量，因此将 `lower` 和 upper 字段定义为 volatile 类型不能够充分实现类的线程安全；从而仍然需要使用同步。否则，如果凑巧两个线程在同一时间使用不一致的值执行 `setLower` 和 `setUpper` 的话，则会使范围处于不一致的状态。例如，如果初始状态是 `(0, 5)`，同一时间内，线程 A 调用 `setLower(4)` 并且线程 B 调用 `setUpper(3)`，显然这两个操作交叉存入的值是不符合条件的，那么两个线程都会通过用于保护不变式的检查，使得最后的范围值是 `(4, 3)` —— 一个无效值。至于针对范围的其他操作，我们需要使 `setLower()` 和 `setUpper()` 操作原子化 —— 而将字段定义为 volatile 类型是无法实现这一目的的。

---



### Java线程的几大状态及转换. 

[Java线程的5种状态及状态间的转换](https://blog.csdn.net/u011296485/article/details/77910087)

```
1. 新建(new)：新创建了一个线程对象。

2. 可运行(runnable)：线程对象创建后，其他线程(比如main线程）调用了该对象的start()方法。该状态的线程位于可运行线程池中，等待被线程调度选中，获取cpu 的使用权 。

3. 运行(running)：可运行状态(runnable)的线程获得了cpu 时间片（timeslice） ，执行程序代码。

4. 阻塞(block)：阻塞状态是指线程因为某种原因放弃了cpu 使用权，也即让出了cpu timeslice，暂时停止运行。直到线程进入可运行(runnable)状态，才有机会再次获得cpu timeslice 转到运行(running)状态。阻塞的情况分三种：

	4.1 等待阻塞：运行(running)的线程执行o.wait()方法，JVM会把该线程放入等待队列(waitting queue)中。

	4.2 同步阻塞：运行(running)的线程在获取对象的同步锁时，若该同步锁被别的线程占用，则JVM会把该线程放入锁池(lock pool)中。

	4.3 其他阻塞：运行(running)的线程执行Thread.sleep(long ms)或t.join()方法，或者发出了I/O请求时，JVM会把该线程置为阻塞状态。当sleep()状态超时、join()等待线程终止或者超时、或者I/O处理完毕时，线程重新转入可运行(runnable)状态。

5. 死亡(dead)：线程run()、main() 方法执行结束，或者因异常退出了run()方法，则该线程结束生命周期。死亡的线程不可再次复生。

```



![](https://img-blog.csdn.net/20170909102514784?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTI5NjQ4NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



### 线程可重入是什么概念

[关于线程安全和可重入的区别，写得很好所以转过来了](https://blog.csdn.net/xiarendeniao/article/details/7277709)

按照Wiki上的说法，“A computer program or routine is described as reentrant if it can be safely executed concurrently; that is, the routine can be re-entered while it is already running.”根据笔者的经验，

所谓“重入”，常见的情况是，程序执行到某个函数foo()时，收到信号，于是暂停目前正在执行的函数，转到信号处理 函数，而这个信号处理函数的执行过程中，又恰恰也会进入到刚刚执行的函数foo()，这样便发生了所谓的重入。此时如果foo()能够正确的运行，而且处 理完成后，之前暂停的foo()也能够正确运行，则说明它是可重入的。

• 确保可重入：         

要确保函数可重入，需满足以下几个条件：

```
1、不在函数内部使用静态或全局数据        

2、不返回静态或全局数据，所有数据都由函数的调用者提供。         

3、使用本地数据，或者通过制作全局数据的本地拷贝来保护全局数据。        

4、不调用不可重入函数。 
```

• 不可重入的后果：        

不可重入的后果主要体现在象信号处理函数这样需要重入的情况中。如果信号处理函数中使用了不可重入的函数，则可能导致程序的错误甚至崩溃。          

* 可重入与线程安全

​       可重入与线程安全并不等同。一般说来，可重入的函数一定是线程安全的，但反过来不一定成立。它们的关系可用下图来表示：
![img](http://dl.iteye.com/upload/attachment/296893/e7fd1fc2-f4ed-3e69-aaa4-7da720043c91.jpg)	

​       我们可以采用下面的变化过程来进一步说明上图： 

```
-如果一个函数中用到了全局或静态变量，那么它不是线程安全的，也不是可重入的； 

- 如果我们对它加以改进，在访问全局或静态变量时使用互斥量或信号量等方式加锁，则可以使它变成线程安全的，但此时它仍然是不可重入的，因为通常加锁方式是针对不同线程的访问，而对同一线程可能出现问题；

- 如果将函数中的全局或静态变量去掉，改成函数参数等其他形式，则有可能使函数变成既线程安全，又可重入。 
```

比如：strtok函数是既不可重入的，也不是线程安全的；加锁的strtok不是可重入的，但线程安全；而strtok_r既是可重入的，也是线程安全的。



### 可重入锁 

[究竟什么是可重入锁？](https://blog.csdn.net/rickiyeat/article/details/78314451)

* 释义

广义上的可重入锁指的是可重复可递归调用的锁，在外层使用锁之后，在内层仍然可以使用，并且不发生死锁（前提得是同一个对象或者class），这样的锁就叫做可重入锁。

ReentrantLock和synchronized都是可重入锁，下面是一个用synchronized实现的例子：

```
public class ReentrantTest implements Runnable {

    public synchronized void get() {
        System.out.println(Thread.currentThread().getName());
        set();
    }

    public synchronized void set() {
        System.out.println(Thread.currentThread().getName());
    }

    public void run() {
        get();
    }

    public static void main(String[] args) {
        ReentrantTest rt = new ReentrantTest();
        for(;;){
            new Thread(rt).start();
        }
    }
}

```

整个过程没有发生死锁的情况，截取一部分输出结果如下：

```
Thread-8492
Thread-8492
Thread-8494
Thread-8494
Thread-8495
Thread-8495
Thread-8493
Thread-8493
```

set()和get()同时输出了线程名称，表明即使递归使用synchronized也没有发生死锁，证明其是可重入的。

*  不可重入锁

不可重入锁，与可重入锁相反，不可递归调用，递归调用就发生死锁。看到一个经典的讲解，使用自旋锁来模拟一个不可重入锁，代码如下：

```
import java.util.concurrent.atomic.AtomicReference;

public class UnreentrantLock {

    private AtomicReference<Thread> owner = new AtomicReference<Thread>();

    public void lock() {
        Thread current = Thread.currentThread();
        //这句是很经典的“自旋”语法，AtomicInteger中也有
        for (;;) {
            if (!owner.compareAndSet(null, current)) {
                return;
            }
        }
    }

    public void unlock() {
        Thread current = Thread.currentThread();
        owner.compareAndSet(current, null);
    }
}

```

在执行每次操作之前，判断当前锁持有者是否是当前对象，采用state计数，不用每次去释放锁。

### 如果要设计一个线程池, 需要考虑哪些要素. 

[[014]你想自己设计一个线程池么](https://www.jianshu.com/p/e401a5d1b2cb	)

* 线程池的作用

```markdown
1.线程池即预先创建线程的技术，一个线程执行完后重新放回不会销毁掉提高了线程的利用率。

2.由于我们要使用线程来执行任务的时候直接从线程池中去现成的所以提高了程序的相应速度。

3.线程池可以对里面的线程进行管理，至于如何管理XXXX（如何销毁线程、如何结束线程状态等等）。

```

* 创建线程池需要考虑的

从这里我们知道线程池的一些基本配置参数。

比如 线程池的大小，执行任务队列，线程池满了新任务的执行策略，工作线程空闲后存活时间（如果想提高线程利用率提议调大该时间）。
所以它的构造函数为：

```
public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue) 
```

> 这里为什么有一个corePoolSize和maximumPoolSize呢？ 

级别关系是 coreSize -> 队列 ->(无法使用队列则创建新线程) maximumPoolSize。

a).corePoolSize意思是基本大小，

比如线程池corePoolSize=10，而此时线程池里有5个线程且都是空闲的，由于还没有达到corePoolSize，如果提交一个任务会从线程池里选择一个线程来执行任务。

当达到了corePoolSize时Executor默认会先把任务添加进队列中，如果无法加入队列则创建新线程直到达到maximumPoolSize。

 b).maximumPoolSize使用场景，如果线程池里的线程数量达到了maximumPoolSize且其中的线程没有空闲的。当新任务到达的时候会新建线程，如果无限地创建会消耗系统的资源，所以这里有一个maximumPoolSize参数，当线程数量达到maximumPoolSize的时候即时没有空闲线程了也不会重新创建线程。

不重新创建线程那怎么办呢？

这就需要使用RejectedExecutionHandler（饱和策略）。

现有的饱和策略有,策略分两种执行与不执行：

```
对于不执行的，我可能会有以下情况：a.丢弃  b.抛出异常 c.丢弃但是记录日志或持久化到数据库（通过实现RejectedExecutionHandler接口来处理）。

对于执行该任务会有如下的情况：
a.腾出空间，替换最老未执行的任务。

 1).丢弃该任务  2).丢弃最老未执行的腾出空间执行该任务

```

#### 加入队列的几种情况

当我们创建线程池需要指定队列的时候必须，而不同队列线程池会有不同的表现。

 有3种常见的队列：
 a).ArrayBlockingQueue 有界队列，创建时候必须制定大小(构造函数要求制定)
 b).LinkedBlockingQueue 无界队列

```
 public LinkedBlockingQueue() {
 }
```

c).SynchronousQueue 同步队列，每新增一个任务的线程必须等待另一个线程取出任务。 //还是不是很理解同步队列怎么实现的背后的实现原理-怎么做到同步的。

*  这3种队列的使用场景是什么？

 当资源有限的时候使用有界队列，使用有界队列的过程中，队列大小和最大池大小可能需要互相折衷。大队列小线程池大小可以降低CPU使用率和线程之间的切换。

 使用无界队列时候maxSize参数无用，因为当线程数超过coresize的时候会一直不停的往LinkedBlockingQueue里放。这个可以用于web服务器访问量突发的情况。

#### 线程池如何处理任务

这里讲线程池如何提交任务，任务提交后如何跟踪结果。
 execute方式提交，这里没有返回结果。所以无法获取任务执行结果。

```
public void execute(Runnable command) {
    ......
    addWorke(command,true)
    }

private boolean addWorker(Runnable firstTask, boolean core) {
     w = new Worker(firstTask); //这里会把Runable接口包装城worker接口
     works.add(w);
}
```

addWorker 怎么判断线程池已经满了涉及到二进制操作，以后专门写博客来阐述。

submit()方式提交可以通过future获取任务执行结果，当调用future.get()时候如果任务执行未完成则会阻塞。

```
<T> Future<T> submit(Callable<T> task);
```

####  线程池如何关闭

线程池关闭的时候需要考虑其所处的状态，即如果有任务未执行完怎么办？什么时候应该关闭线程池。

> 常见的办法就是一个个遍历线程，如果不等待执行完就sotp停止线程或者中断现在执行的线程。

 线程池关闭的状态中有几个中间状态可以根据 队列是否有正在执行的线程，有的话是否继续执行来划分。

线程池的状态有：

```
Running 可以接收新的任务和执行队列任务,
shutdown 不接收新的任务和已有队列任务还需要执行,
stop 不接收新任务且 已有队列任务也停止（interrupt in-process task）
terminate 线程池已经停止了
```

这里shutdown()与shutdownNow的区别就是shutdown只会interrputIdleWork，即只会终端没有非运行时的线程，正在执行的线程等待执行完。
 代码区别如下：

```
public List<Runnable> shutdownNow() {
        List<Runnable> tasks;
        final ReentrantLock mainLock = this.mainLock;
        mainLock.lock();
        try {
            checkShutdownAccess();
            advanceRunState(STOP);
            interruptWorkers();
            tasks = drainQueue();
        } finally {
            mainLock.unlock();
        }
        tryTerminate();
        return tasks;
    }

public void shutdown() {
        final ReentrantLock mainLock = this.mainLock;
        mainLock.lock();
        try {
            checkShutdownAccess();
            advanceRunState(SHUTDOWN);
            interruptIdleWorkers();
            onShutdown(); // hook for ScheduledThreadPoolExecutor
        } finally {
            mainLock.unlock();
        }
        tryTerminate();
    }

```



### Executors工厂类能创建哪些线程池, 用过哪些?

[Executors类中创建线程池的几种方法的分析](https://blog.csdn.net/u010412719/article/details/52489843)

要配置一个线程池是比较复杂的，尤其是对于线程池的原理不是很清楚的情况下，很有可能配置的线程池不是较优的，因此在Executors类里面提供了一些静态工厂，生成一些常用的线程池。

 1、newFixedThreadPool：创建固定大小的线程池。线程池的大小一旦达到最大值就会保持不变，如果某个线程因为执行异常而结束，那么线程池会补充一个新线程。

    /*
    函数功能：创建一个固定长度的的线程池，用于保存任务的阻塞队列为无限制长度的LinkedBlockingQueue。
              线程池中的线程将会一直存在除非线程池shutdown，即线程池中的线程没有受到存活时间的限制。
    */
    public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
    }
    
    /*
        函数功能：创建指定线程数的线程池，并且指定了线程工厂来生产新的线程。任务队列无界。
    */
    public static ExecutorService newFixedThreadPool(int nThreads, ThreadFactory threadFactory) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>(),
                                      threadFactory);
    }
2、newCachedThreadPool：创建一个可缓存的线程池。如果线程池的大小超过了处理任务所需要的线程，那么就会回收部分空闲（60秒不执行任务）的线程，当任务数增加时，此线程池又可以智能的添加新线程来处理任务。此线程池不会对线程池大小做限制，线程池大小完全依赖于操作系统（或者说JVM）能够创建的最大线程大小。

在Executors类中此方法的代码如下：

    /*
        函数功能：创建一个线程池，这个线程池的corePoolSize的大小为零，maxPoolSize为Integer.MAX_VALUE.
            即对于线程池，只要需要就可以创建新的线程。不过如果有空闲线程存在则就会重用此线程。
            此线程池的线程都有一定的存活时间。即如果线程已经有60s没有被使用则就会被移除线程池。
    */
    public static ExecutorService newCachedThreadPool() {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>());
    }
    
    //与上面的功能一样，只是指定了线程工厂。
    public static ExecutorService newCachedThreadPool(ThreadFactory threadFactory) {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>(),
                                      threadFactory);
    }
3、newSingleThreadExecutor：创建一个单线程的线程池。这个线程池只有一个线程在工作，也就是相当于单线程串行执行所有任务。如果这个唯一的线程因为异常结束，那么会有一个新的线程来替代它。此线程池保证所有任务的执行顺序按照任务的提交顺序执行。

    //创建一个单工作线程且无边界的队列的线程池。如果执行期间此线程挂掉了，则如果需要就会产生一个新线程来顺序执行任务。
    //任何时候都不会有超过一个线程是存活的
    public static ExecutorService newSingleThreadExecutor() {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>()));
    }
    
    /*
        函数功能：与newSingleThreadExecutor()功能一样，只是指定了线程工厂来生产新的线程。
    */
    public static ExecutorService newSingleThreadExecutor(ThreadFactory threadFactory) {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>(),
                                    threadFactory));
    }

4、newScheduledThreadPool：创建一个大小无限的线程池。此线程池支持定时以及周期性执行任务的需求。

    //创建一个线程池，该线程池提供延时执行任务或者是周期性执行任务的功能。
    public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize) {
        return new ScheduledThreadPoolExecutor(corePoolSize);
    }
    
    public static ScheduledExecutorService newScheduledThreadPool(
            int corePoolSize, ThreadFactory threadFactory) {
        return new ScheduledThreadPoolExecutor(corePoolSize, threadFactory);
    }
ScheduledThreadPoolExecutor 类中的构造函数如下：

    /*
        函数功能：根据指定的corePoolSize的大小来创建线程池，maxPoolSize的大小为Integer.MAX_VALUE，线程没有存活时间的限制
    */
    public ScheduledThreadPoolExecutor(int corePoolSize) {
        super(corePoolSize, Integer.MAX_VALUE, 0, NANOSECONDS,
              new DelayedWorkQueue());
    }
5、newSingleThreadScheduledExecutor：创建一个单线程的线程池。此线程池支持定时以及周期性执行任务的需求。

    //创建一个单线程的线程池，提供任务延时执行或周期性执行的功能。
    //如果该线程在线程池运行期间挂掉，则如果需要则会产生一个新的线程来顺序执行任务。
    public static ScheduledExecutorService newSingleThreadScheduledExecutor() {
        return new DelegatedScheduledExecutorService
            (new ScheduledThreadPoolExecutor(1));
    }
    
    public static ScheduledExecutorService newSingleThreadScheduledExecutor(ThreadFactory threadFactory) {
        return new DelegatedScheduledExecutorService
            (new ScheduledThreadPoolExecutor(1, threadFactory));
    }
ScheduledThreadPoolExecutor 类中的构造函数如下：

    /*
        函数功能：根据指定的corePoolSize的大小来创建线程池，maxPoolSize的大小为Integer.MAX_VALUE.
    */
    public ScheduledThreadPoolExecutor(int corePoolSize) {
        super(corePoolSize, Integer.MAX_VALUE, 0, NANOSECONDS,
              new DelayedWorkQueue());
    }
---

### 讲一讲熟悉的设计模式. 单例模式及工厂模式的实现方法. 

[单例、多例模式&&工厂模式](https://blog.csdn.net/hackerHL/article/details/56485973)

假设，老张开车去[东北](https://www.baidu.com/s?wd=%E4%B8%9C%E5%8C%97&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)

```
public class Car {
    public void run(){      
      System.out.println("冒着烟奔跑中car.........");
    }
}
```

```
public class Test {

    public static void main(String[] args) {    

        //如果把new交给司机，那么司机想new多少car就能有多少。

        Car c=new Car();

        c.run();        

    }
}
```

想要控制只给老张一辆车，不让他随便new出来，可以把Car的构造函数设为private，Car自主生产一个实例，不再依赖于new，封装一个方法，让Car定义一个方法返回唯一实例。

* 单例模式

```
public class Car {  
    private static Car car=new Car();   //实例
    private Car(){  }   //构造函数私有

    //单例模式的getInstance方法==静态工厂方法
    public static Car getInstance(){
       return car;  
    }   
    public void run(){
        System.out.println("冒着烟奔跑中car.........");
    }
}

```

测试： 
因为Car的getInstance（）方法返回static变量，是同一个实例，所以c1和c2是同一个对象。

```
public class Test { 
    public static void main(String[] args) {        
        Car c1=Car.getInstance();
        Car c2=Car.getInstance();
        if(c1==c2){
            System.out.print("same car");
        }       
    }
}
```

如果需要在Car中产生多个实例，不只是一个实例，可以用多例模式。  比如JDBC的连接池，从池中选择一条连接使用，使用后回收到池中，Connection就是多例

* 多例模式

在Car中定义`List<Car>`成员变量

```
private static List<Car> cars=new ArrayList<Car>();
```

再把cars初始化，当需要使用的时候，就从cars中选择一个使用。

* 工厂模式

new产生实例，如果想对对象的生产过程也能够进行定制，有好多可扩展性，且能封装生产过程，使用工厂模式

* 1、简单工厂模式

```
//Moveable接口用于实现run

public class Car implements Moveable{

    @Override

    public void run() {

        System.out.print("开汽车\n");

    }

} 

```

```
//Moveable接口用于实现run

public class Plane implements Moveable {

    @Override

    public void run() {

        System.out.print("开飞机\n");      

    }

}

```



工厂

```
public class CarFactory {
    public Car createCar(){
        return new Car();
    }
}
```

```
public class PlaneFactory {
    public Plane createPlane(){
        return new Plane();
    }
}
```

```
public class Test { 
    public static void main(String[] args) {
        PlaneFactory planeFactory=new PlaneFactory();
        Plane p=planeFactory.createPlane();//从工厂获取
        p.run();        
    }
}

```

简单工厂模式的工厂可扩展性不好，如果想要替换工厂，则换掉的工厂方法也得改，可扩展性不好

* 2、抽象工厂模式

产生交通工具的抽象工厂

```
public abstract class VehicleFactory {
    abstract Moveable create();
}
```

继承自抽象工厂类的具体工厂（子工厂）

```
public class CarFactory extends VehicleFactory{
    @Override
    Moveable create() {
        return new Car();
    }
}
```

```
public class PlaneFactory extends VehicleFactory{
    @Override
    Moveable create() {
        return new Plane();
    }
}
```

```
public class Test { 
    public static void main(String[] args) {
        VehicleFactory factory=new PlaneFactory();
        Plane p=(Plane) factory.create();//多态，会隐藏具体实现方法
        p.run();

        factory=new CarFactory();
        Car c=(Car) factory.create();//多态，会隐藏具体实现方法
        c.run();
    }
}

```

不仅可以控制对象，还可以控制对象的生产过程。  需要修改的只有`VehicleFactory factory=new PlaneFactory();`，需要生产什么对象，就new出相应的工厂，如果有配置文件，连这个都不用修改  

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20170222160355257?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGFja2VySEw=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**体会：产生对象的方法，就是工厂方法，不一定局限于是在哪个位置，是哪个范畴，学习到最后要灵活运用，手中无剑心中有剑**

getInstance和Factory在[jdk](https://www.baidu.com/s?wd=jdk&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)中常用，还有abstractFactory也非常常用

* 3、配置文件

对于普通工厂来说，产生产品系列时，会产生工厂泛滥的问题，对于抽象工厂来说，也有一个问题：比如抽象工厂类，当它需要在抽象类中添加或者删去一个方法（即改变产品时），就需要改动所有继承自该厂的所有子类的方法，改动的地方太多 

spring提供了一种方案： 

```
1、spring提供的是bean工厂（BeanFactory) 

2、原先通过new方式得到的实例Bean对象，现在通过配置文件的配置来配置实例

```



* 模拟spring 

1、配置文件spring.properties

```
VehicleType=com.soto.spring.Train  //  键值对
```

2、通过java的Properties、反射来获取实例对象

```java
package com.HL.spring;

import java.io.IOException;

import java.util.Properties;

public class Test {

    public static void main(String[] args) throws IOException, ClassNotFoundException, InstantiationException, IllegalAccessException {

        //用来读配置文件的
        Properties pros=new Properties();
        //Test.class.getClassLoader():拿到装载类对象的class装载器,getResourceAsStream：把文件当成流
        pros.load(Test.class.getClassLoader().getResourceAsStream("com/HL/spring/spring.properties"));
    
        //获取key为VehicleType的值
        String vehicleTypeName=pros.getProperty("VehicleType");
        System.out.println(vehicleTypeName);
    
        //运用java的反射机制，通过字符串得到实例
        Object o=Class.forName(vehicleTypeName).newInstance();
        Moveable m=(Moveable)o;
        m.run();    
    }
    

}

```

* 4、Spring

Spring中的运用：使用application.xml作为全局配置文件 
**application.xml中的配置信息**

```java
<Bean id="v" class="com.soto.spring.Car" />	
```

**加载配置文件，用ClassPathXmlApplication**

```java
BeanFactory f=new ClassPathXmlApplicationContext("application.xml");//加载配置文件中配置的Bean类
```

java解析XML文件，有多种方法，比如JDOM、SAX等，可以搜索一下JDOM/XPATH编程指南. 
spring中加载完xml配置文件之后，会把配置文件中声明的Bean放在List中，然后放进Map中，即Bean容器（IOC），提供给应用程序（程序通过getBean根据id得到Bean），即AOP

```
Object o=f.getBean("v");//根据id获取相应ID的Bean实例
Moveable m=(Moveable)o;
m.run();
```

这就是Bean容器，即IOC。



---

### 装饰器模式是怎么一回事

[装饰器模式](http://www.runoob.com/design-pattern/decorator-pattern.html)

装饰器模式（Decorator Pattern）允许向一个现有的对象添加新的功能，同时又不改变其结构。这种类型的设计模式属于结构型模式，它是作为现有的类的一个包装。

这种模式创建了一个装饰类，用来包装原有的类，并在保持类方法签名完整性的前提下，提供了额外的功能。

我们通过下面的实例来演示装饰器模式的用法。其中，我们将把一个形状装饰上不同的颜色，同时又不改变形状类。

#### 介绍

**意图：**动态地给一个对象添加一些额外的职责。就增加功能来说，装饰器模式相比生成子类更为灵活。

**主要解决：**一般的，我们为了扩展一个类经常使用继承方式实现，由于继承为类引入静态特征，并且随着扩展功能的增多，子类会很膨胀。

**何时使用：**在不想增加很多子类的情况下扩展类。

**如何解决：**将具体功能职责划分，同时继承装饰者模式。

**关键代码：** 1、Component 类充当抽象角色，不应该具体实现。 2、修饰类引用和继承 Component 类，具体扩展类重写父类方法。

**应用实例：** 1、孙悟空有 72 变，当他变成"庙宇"后，他的根本还是一只猴子，但是他又有了庙宇的功能。 2、不论一幅画有没有画框都可以挂在墙上，但是通常都是有画框的，并且实际上是画框被挂在墙上。在挂在墙上之前，画可以被蒙上玻璃，装到框子里；这时画、玻璃和画框形成了一个物体。

**优点：**装饰类和被装饰类可以独立发展，不会相互耦合，装饰模式是继承的一个替代模式，装饰模式可以动态扩展一个实现类的功能。

**缺点：**多层装饰比较复杂。

**使用场景：** 1、扩展一个类的功能。 2、动态增加功能，动态撤销。

**注意事项：**可代替继承。

#### 实现

我们将创建一个 *Shape* 接口和实现了 *Shape* 接口的实体类。然后我们创建一个实现了 *Shape* 接口的抽象装饰类 *ShapeDecorator*，并把 *Shape* 对象作为它的实例变量。

*RedShapeDecorator* 是实现了 *ShapeDecorator* 的实体类。

*DecoratorPatternDemo*，我们的演示类使用 *RedShapeDecorator* 来装饰 *Shape* 对象。

![è£é¥°å¨æ¨¡å¼ç UML å¾](http://www.runoob.com/wp-content/uploads/2014/08/decorator_pattern_uml_diagram.jpg)

* 步骤 1

创建一个接口：

>  Shape.javaape.java

```java
public interface Shape {
   void draw();
}
```

* 步骤 2

创建实现接口的实体类。

> Rectangle.java

```java
public class Rectangle implements Shape {

   @Override

   public void draw() {

      System.out.println("Shape: Rectangle");

   }

}

```

>  Circle.java



```java
public class Circle implements Shape {

   @Override

   public void draw() {

      System.out.println("Shape: Circle");

   }

}

```

* 步骤 3

创建实现了 Shape 接口的抽象装饰类。

> ShapeDecorator.java

```java
public abstract class ShapeDecorator implements Shape {

   protected Shape decoratedShape;

   public ShapeDecorator(Shape decoratedShape){

      this.decoratedShape = decoratedShape;

   }

   public void draw(){

      decoratedShape.draw();

   }  

}

```

* 步骤 4

创建扩展了 ShapeDecorator 类的实体装饰类。

> RedShapeDecorator.java

```java
public class RedShapeDecorator extends ShapeDecorator {

   public RedShapeDecorator(Shape decoratedShape) {

      super(decoratedShape);     

   }

   @Override

   public void draw() {

      decoratedShape.draw();         

      setRedBorder(decoratedShape);

   }

   private void setRedBorder(Shape decoratedShape){

      System.out.println("Border Color: Red");

   }

}
	
```

* 步骤 5

使用 RedShapeDecorator 来装饰 Shape 对象。

>  DecoratorPatternDemo.java

```java
public class DecoratorPatternDemo {

   public static void main(String[] args) {

      Shape circle = new Circle();
     
      Shape redCircle = new RedShapeDecorator(new Circle());
     
      Shape redRectangle = new RedShapeDecorator(new Rectangle());
      System.out.println("Circle with normal border");
      circle.draw();
     
      System.out.println("\nCircle of red border");
      redCircle.draw();
     
      System.out.println("\nRectangle of red border");
      redRectangle.draw();
    

   }

}

```

* 步骤 6

执行程序，输出结果：

```bash
Circle with normal border

Shape: Circle

Circle of red border

Shape: Circle

Border Color: Red

Rectangle of red border

Shape: Rectangle

Border Color: Red

```

---

### 讲一讲熟悉的JVM GC算法

内存区域中的**程序计数器、虚拟机栈、本地方法栈**这3个区域随着线程而生，线程而灭；

**栈中的栈帧**随着方法的进入和退出而有条不紊地执行着出栈和入栈的操作，每个栈帧中分配多少内存基本是**在类结构确定下来时就已知**的。**在这几个区域不需要过多考虑回收的问题，因为方法结束或者线程结束时，内存自然就跟着回收了**。

而**Java堆和方法区**则不同，一个接口中的多个实现类需要的内存可能不同，一个方法中的多个分支需要的内存也可能不一样，我们只有在程序处于运行期间时才能知道会创建哪些对象，**这部分内存的分配和回收都是动态的**，GC关注的也是这部分内存，后面的文章中如果涉及到“内存”分配与回收也仅指这一部分内存。

**二. 引用计数算法：（老牌垃圾回收算法。无法处理循环引用，没有被Java采纳）**

**1、引用计数算法的概念：**

给对象中添加一个引用计数器，每当有一个地方引用它时，计数器值就加1；当引用失效时，计数器值就减1；任何时刻计数器为0的对象就是不可能再被使用的。

![e6217360-0985-46e8-88fd-022f1fc0fba5](https://images0.cnblogs.com/blog/641601/201508/201002451136455.png)

**2、使用者举例：**

引用计数算法的实现简单，判定效率也高，大部分情况下是一个不错的算法。很多地方应用到它。例如：

> 微软公司的COM技术：Computer Object Model
>
> 使用ActionScript3的FlashPlayer
>
> Python

但是，主流的java虚拟机并没有选用引用计数算法来管理内存，其中最主要的原因是：**它很难解决对象之间相互循环引用的问题**。

**3、引用计数算法的问题：**

- 引用和去引用伴随加法和减法，影响性能
- 致命的缺陷：对于**循环引用的对象**无法进行回收

 ![1a489e67-e047-408f-a97e-4a141e6ab3b0](https://images0.cnblogs.com/blog/641601/201508/201002460667613.png)

上面的3个图中，对于最右边的那张图而言：循环引用的计数器都不为0，但是他们对于根对象都已经不可达了，但是无法释放。

**三、根搜索算法：**

**1、根搜索算法的概念：**

　　由于引用计数算法的缺陷，所以JVM一般会采用一种新的算法，叫做**根搜索算法**。它的处理方式就是，**设立若干种根对象，当任何一个根对象到某一个对象均不可达时，则认为这个对象是可以被回收的**。

[![7ab0f17b-13f7-4886-a24d-3813c2173891](https://images0.cnblogs.com/blog/641601/201508/201002477226386.jpg)](http://images0.cnblogs.com/blog/641601/201508/201002468169242.jpg)

如上图所示，ObjectD和ObjectE是互相关联的，但是由于GC roots到这两个对象不可达，所以最终D和E还是会被当做GC的对象，上图若是采用引用计数法，则A-E五个对象都不会被回收。

**2、可达性分析：**

 我们刚刚提到，设立若干种根对象，当任何一个根对象到某一个对象均不可达时，则认为这个对象是可以被回收的。我们在后面介绍标记-清理算法/标记整理算法时，也会一直强调从根节点开始，对所有可达对象做一次标记，那什么叫做可达呢？这里解释如下：

**可达性分析：**

　　从根（GC Roots）的对象作为起始点，开始向下搜索，搜索所走过的路径称为“**引用链**”，当一个对象到GC Roots没有任何引用链相连（用图论的概念来讲，就是从GC Roots到这个对象不可达）时，则证明此对象是不可用的。

**3、根（GC Roots）：**

说到GC roots（GC根），在JAVA语言中，可以当做GC roots的对象有以下几种：

> **1、栈（栈帧中的本地变量表）中引用的对象。**
>
> **2、方法区中的静态成员。**
>
> **3、方法区中的常量引用的对象（全局变量）**
>
> **4、本地方法栈中JNI（一般说的Native方法）引用的对象。**

注：第一和第四种都是指的方法的本地变量表，第二种表达的意思比较清晰，第三种主要指的是声明为final的常量值。

在根搜索算法的基础上，现代虚拟机的实现当中，**垃圾搜集的算法**主要有三种，分别是**标记-清除算法、复制算法、标记-整理**算法。这三种算法都扩充了根搜索算法，不过它们理解起来还是非常好理解的。

**四、标记-清除算法：**

**1、标记清除算法的概念：**

标记-清除算法是现代垃圾回收算法的思想基础。标记-清除算法将垃圾回收分为两个阶段：标记阶段和清除阶段。一种可行的实现是，在标记阶段，**首先通过根节点，标记所有从根节点开始的可达对象**。因此，未被标记的对象就是未被引用的垃圾对象；然后，在清除阶段，清除所有未被标记的对象。

[![7de44970-2e02-46a1-a5d0-0663b21906c6](https://images0.cnblogs.com/blog/641601/201508/201002497066258.png)](http://images0.cnblogs.com/blog/641601/201508/201002487061086.png)

**2、标记-清除算法详解：**

它的做法是当堆中的有效内存空间（available memory）被耗尽的时候，就会停止整个程序（也被成为stop the world），然后进行两项工作，第一项则是标记，第二项则是清除。

- 标记：标记的过程其实就是，遍历所有的GC Roots，然后将所有GC Roots可达的对象标记为存活的对象。
- 清除：清除的过程将遍历堆中所有的对象，将没有标记的对象全部清除掉。

也就是说，**就是当程序运行期间，若可以使用的内存被耗尽的时候，GC线程就会被触发并将程序暂停，随后将依旧存活的对象标记一遍，最终再将堆中所有没被标记的对象全部清除掉，接下来便让程序恢复运行**。

来看下面这张图：

![47146934-c3a3-4976-991f-77e84ae008cc](https://images0.cnblogs.com/blog/641601/201508/201002514255816.jpg) 

上图代表的是程序运行期间所有对象的状态，**它们的标志位全部是0**（也就是未标记，以下默认0就是未标记，1为已标记），假设这会儿**有效内存空间耗尽**了，JVM将会停止应用程序的运行并开启GC线程，然后开始进行标记工作，按照根搜索算法，标记完以后，对象的状态如下图：

![5cbf57ce-c83a-40d2-b58a-b37d3eee3803](https://images0.cnblogs.com/blog/641601/201508/201002528002803.jpg)

上图中可以看到，按照根搜索算法，所有从root对象可达的对象就被标记为了存活的对象，此时已经完成了第一阶段标记。接下来，就要执行第二阶段清除了，那么清除完以后，剩下的对象以及对象的状态如下图所示：

[![8654ed59-fc00-446d-8995-a02ab57cf213](https://images0.cnblogs.com/blog/641601/201508/201002549412892.jpg)](http://images0.cnblogs.com/blog/641601/201508/201002539108475.jpg)

上图可以看到，没有被标记的对象将会回收清除掉，而被标记的对象将会留下，并且会将标记位重新归0。接下来就不用说了，唤醒停止的程序线程，让程序继续运行即可。

 **疑问：为什么非要停止程序的运行呢？**

答：

```
这个其实也不难理解，假设我们的程序与GC线程是一起运行的，各位试想这样一种场景。

假设我们刚标记完图中最右边的那个对象，暂且记为A，结果此时在程序当中又new了一个新对象B，且A对象可以到达B对象。但是由于此时A对象已经标记结束，B对象此时的标记位依然是0，因为它错过了标记阶段。因此当接下来轮到清除阶段的时候，新对象B将会被苦逼的清除掉。如此一来，不难想象结果，GC线程将会导致程序无法正常工作。

上面的结果当然令人无法接受，我们刚new了一个对象，结果经过一次GC，忽然变成null了，这还怎么玩？

```

**3、标记-清除算法的缺点：**

（1）首先，**它的缺点就是效率比较低（递归与全堆对象遍历）**，导致stop the world的时间比较长，尤其对于交互式的应用程序来说简直是无法接受。试想一下，如果你玩一个网站，这个网站一个小时就挂五分钟，你还玩吗？

（2）第二点主要的缺点，则是**这种方式清理出来的空闲内存是不连续的**，这点不难理解，我们的死亡对象都是随即的出现在内存的各个角落的，现在把它们清除之后，内存的布局自然会乱七八糟。而为了应付这一点，JVM就不得不维持一个内存的空闲列表，这又是一种开销。而且在分配数组对象的时候，寻找连续的内存空间会不太好找。

 **五、复制算法：（新生代的GC）**

**复制算法的概念：**

将原有的内存空间分为两块，每次只使用其中一块，在垃圾回收时，将正在使用的内存中的存活对象复制到未使用的内存块中，之后，清除正在使用的内存块中的所有对象，交换两个内存的角色，完成垃圾回收。

- 与标记-清除算法相比，复制算法是一种相对高效的回收方法
- 不适用于存活对象较多的场合，如老年代（复制算法**适合做新生代的GC**）

![ff1e1846-e49c-4663-aee1-7c63628f567c](https://images0.cnblogs.com/blog/641601/201508/201002560357091.png)

- **复制算法的最大的问题是：空间的浪费**

复制算法使得每次都只对整个半区进行内存回收，内存分配时也就不用考虑内存碎片等复杂情况，只要移动堆顶指针，按顺序分配内存即可，实现简单，运行高效。只是这种算法的代价是将内存缩小为原来的一半，这个太要命了。

所以从以上描述不难看出，复制算法要想使用，最起码对象的存活率要非常低才行，而且最重要的是，我们必须要克服50%内存的浪费。

现在的商业虚拟机都采用这种收集算法来回收新生代，新生代中的对象98%都是“朝生夕死”的，所以并不需要按照1:1的比例来划分内存空间，而是**将内存分为一块比较大的Eden空间和两块较小的Survivor空间**，每次使用Eden和其中一块Survivor。当回收时，将Eden和Survivor中还存活着的对象一次性地复制到另外一块Survivor空间上，最后清理掉Eden和刚才用过的Survivor空间。HotSpot虚拟机默认Eden和Survivor的大小比例是8:1，也就是说，每次新生代中可用内存空间为整个新生代容量的90%（80%+10%），只有10%的空间会被浪费。

当然，98%的对象可回收只是一般场景下的数据，我们没有办法保证每次回收都只有不多于10%的对象存活**，当Survivor空间不够用时，需要依赖于老年代进行分配担保，所以大对象直接进入老年代**。整个过程如下图所示：

![7e1f6ed2-e0c4-45e4-b7db-b59c28e1ee9c](https://images0.cnblogs.com/blog/641601/201508/201002573639064.png)

上图中，绿色箭头的位置代表的是大对象，大对象直接进入老年代。

根据上面的复制算法，现在我们来看下面的这个gc日志的数字，就应该能看得懂了吧：

 ![6d59301f-f0c9-4fed-ba36-e66bc6574e8f](https://images0.cnblogs.com/blog/641601/201508/201002585503293.png)

上方GC日志中，新生代的可用空间是13824K（eden区的12288K+from space的1536K）。而根据内存的地址计算得知，新生代的总空间为15M，而这个15M的空间是 = 13824K +to space 的 1536K。

 **六、标记-整理算法：（老年代的GC）**

**引入：**

​    如果在对象存活率较高时就要进行较多的复制操作，效率将会变低。更关键的是，如果不想浪费50%的空间，就需要有额外的空间进行分配担保，以应对被使用的内存中所有对象都100%存活的极端情况，所以在老年代一般不能直接选中这种算法。

**概念：**

标记-压缩算法适合用于存活对象较多的场合，如老年代。它在标记-清除算法的基础上做了一些优化。和标记-清除算法一样，标记-压缩算法也首先需要从根节点开始，对所有可达对象做一次标记；但之后，它并不简单的清理未标记的对象，而是**将所有的存活对象压缩到内存的一端；**之后，清理边界外所有的空间。

[![cc79889a-0856-4018-92c3-c51108c9caea](https://images0.cnblogs.com/blog/641601/201508/201003005357866.png)](http://images0.cnblogs.com/blog/641601/201508/201002594252194.png)

- **标记：它的第一个阶段与标记/清除算法是一模一样的，均是遍历GC Roots，然后将存活的对象标记。**
- **整理：移动所有存活的对象，且按照内存地址次序依次排列，然后将末端内存地址以后的内存全部回收。因此，第二阶段才称为整理阶段。**

上图中可以看到，标记的存活对象将会被整理，按照内存地址依次排列，而未被标记的内存会被清理掉。如此一来，当我们需要给新对象分配内存时，JVM只需要持有一个内存的起始地址即可，这比维护一个空闲列表显然少了许多开销。

**标记/整理算法不仅可以弥补标记/清除算法当中，内存区域分散的缺点，也消除了复制算法当中，内存减半的高额代价。**

- 但是，标记/整理算法唯一的缺点就是效率也不高。

不仅要标记所有存活对象，还要整理所有存活对象的引用地址。从效率上来说，标记/整理算法要低于复制算法。

**标记-清除算法、复制算法、标记整理算法的总结：**

三个算法都基于根搜索算法去判断一个对象是否应该被回收，而支撑根搜索算法可以正常工作的理论依据，就是语法中变量作用域的相关内容。因此，要想防止内存泄露，最根本的办法就是掌握好变量作用域，而不应该使用C/C++式内存管理方式。

在GC线程开启时，或者说GC过程开始时，它们都要暂停应用程序（stop the world）。

它们的区别如下：（>表示前者要优于后者，=表示两者效果一样）

（1）**效率**：复制算法>标记/整理算法>标记/清除算法（此处的效率只是简单的对比时间复杂度，实际情况不一定如此）。

（2）**内存整齐度**：复制算法=标记/整理算法>标记/清除算法。

（3）**内存利用率**：标记/整理算法=标记/清除算法>复制算法。

注1：可以看到标记/清除算法是比较落后的算法了，但是后两种算法却是在此基础上建立的。

注2：**时间与空间不可兼得。**

**七、分代收集算法：（新生代的GC+老年代的GC）**

当前商业虚拟机的GC都是采用的“分代收集算法”，这并不是什么新的思想，只是**根据对象的存活周期的不同将内存划分为几块儿**。一般是把Java堆分为新生代和老年代：**短命对象归为新生代，长命对象归为老年代**。

- **少量对象存活，适合复制算法**：在**新生代**中，每次GC时都发现有大批对象死去，只有少量存活，那就选用**复制算法**，只需要付出少量存活对象的复制成本就可以完成GC。
- **大量对象存活，适合用标记-清理/标记-整理**：在**老年代**中，因为对象存活率高、没有额外空间对他进行分配担保，就必须使用“**标记-清理**”/“标记-整理”算法进行GC。

注：**老年代的对象中，有一小部分是因为在新生代回收时，老年代做担保，进来的对象；绝大部分对象是因为很多次GC都没有被回收掉而进入老年代**。

**八、可触及性：**

所有的算法，需要能够识别一个垃圾对象，因此需要给出一个可触及性的定义。

**可触及的：**

　　从根节点可以触及到这个对象。

​    　　其实就是从根节点扫描，只要这个对象在引用链中，那就是可触及的。

**可复活的：**

　　一旦所有引用被释放，就是可复活状态

　　因为在finalize()中可能复活该对象

**不可触及的：**

　　在finalize()后，可能会进入不可触及状态

　　不可触及的对象不可能复活

​    　　要被回收。

**九、Stop-The-World：**

**1、Stop-The-World概念：**

　　Java中一种全局暂停的现象。

**全局停顿，所有Java代码停止**，native代码可以执行，但不能和JVM交互

**多半情况下是由于GC引起**。

​    少数情况下由其他情况下引起，如：Dump线程、死锁检查、堆Dump。

 

**2、GC时为什么会有全局停顿？**

​    （1）避免无法彻底清理干净

打个比方：类比在聚会，突然GC要过来打扫房间，聚会时很乱，又有新的垃圾产生，房间永远打扫不干净，只有让大家停止活动了，才能将房间打扫干净。

​    况且，如果没有全局停顿，会给GC线程造成很大的负担，GC算法的难度也会增加，GC很难去判断哪些是垃圾。

　 （2）GC的工作必须在一个能确保**一致性**的快照中进行。

这里的一致性的意思是：在整个分析期间整个执行系统看起来就像被冻结在某个时间点上，不可以出现**分析过程中对象引用关系还在不断变化**的情况，该点不满足的话分析结果的准确性无法得到保证。

这点是导致GC进行时必须停顿所有Java执行线程的其中一个重要原因。

**3、Stop-The-World的 ** **危害：**

> **长时间服务停止，没有响应（将用户正常工作的线程全部暂停掉）**
>
> 遇到HA系统，可能引起主备切换，严重危害生产环境。
>
> 　　备注：HA：High Available, 高可用性集群。

[![d07bb3ea-1235-41d5-9fb1-56b4087d1acf](https://images0.cnblogs.com/blog/641601/201508/201003040817483.png)](http://images0.cnblogs.com/blog/641601/201508/201003035037139.png)

比如上面的这主机和备机：现在是主机在工作，此时如果主机正在GC造成长时间停顿，那么备机就会监测到主机没有工作，于是备机开始工作了；但是主机不工作只是暂时的，当GC结束之后，主机又开始工作了，那么这样的话，主机和备机就同时工作了。**主机和备机同时工作其实是非常危险**的，很有可能会导致应用程序不一致、不能提供正常的服务等，进而影响生产环境。

---

### 常用的垃圾收集器. CMS有什么优缺点

[深入理解JVM(3)——7种垃圾收集器](https://crowhawk.github.io/2017/08/15/jvm_3/)

**如果说收集算法是内存回收的方法论，那么垃圾收集器就是内存回收的具体实现。**Java虚拟机规范中对垃圾收集器应该如何实现并没有任何规定，因此不同的厂商、版本的虚拟机所提供的垃圾收集器都可能会有很大差别，并且一般都会提供参数供用户根据自己的应用特点和要求组合出各个年代所使用的收集器。接下来讨论的收集器基于JDK1.7 Update 14 之后的HotSpot虚拟机（在此版本中正式提供了商用的G1收集器，之前G1仍处于实验状态），该虚拟机包含的所有收集器如下图所示：

![img](https://pic.yupoo.com/crowhawk/56a02e55/3b3c42d2.jpg)

上图展示了7种作用于不同分代的收集器，如果两个收集器之间存在连线，就说明它们可以搭配使用。虚拟机所处的区域，则表示它是属于新生代收集器还是老年代收集器。Hotspot实现了如此多的收集器，正是因为目前并无完美的收集器出现，只是选择对具体应用最适合的收集器。

#### 相关概念

##### 并行和并发

- **并行（Parallel）**：指多条垃圾收集线程并行工作，但此时用户线程仍然处于等待状态。
- **并发（Concurrent）**：指用户线程与垃圾收集线程同时执行（但不一定是并行的，可能会交替执行），用户程序在继续运行。而垃圾收集程序运行在另一个CPU上。

##### 吞吐量（Throughput）

吞吐量就是**CPU用于运行用户代码的时间**与**CPU总消耗时间**的比值，即

**吞吐量 = 运行用户代码时间 /（运行用户代码时间 + 垃圾收集时间）。**

假设虚拟机总共运行了100分钟，其中垃圾收集花掉1分钟，那吞吐量就是99%。

##### Minor GC 和 Full GC

- **新生代GC（Minor GC）**：指发生在新生代的垃圾收集动作，因为Java对象大多都具备朝生夕灭的特性，所以Minor GC非常频繁，一般回收速度也比较快。具体原理见上一篇文章。
- **老年代GC（Major GC / Full GC）**：指发生在老年代的GC，出现了Major GC，经常会伴随至少一次的Minor GC（但非绝对的，在Parallel Scavenge收集器的收集策略里就有直接进行Major GC的策略选择过程）。Major GC的速度一般会比Minor GC慢10倍以上。

#### 新生代收集器

##### Serial收集器

**Serial（串行）**收集器是最基本、发展历史最悠久的收集器，它是采用**复制算法**的**新生代收集器**，曾经（JDK 1.3.1之前）是虚拟机**新生代**收集的唯一选择。它是一个单线程收集器，只会使用一个CPU或一条收集线程去完成垃圾收集工作，更重要的是**它在进行垃圾收集时，必须暂停其他所有的工作线程，直至Serial收集器收集结束为止（“Stop The World”）**。这项工作是由虚拟机在后台自动发起和自动完成的，在用户不可见的情况下把用户正常工作的线程全部停掉，这对很多应用来说是难以接收的。

下图展示了Serial 收集器（老年代采用Serial Old收集器）的运行过程：

![img](https://pic.yupoo.com/crowhawk/6b90388c/6c281cf0.png)

为了消除或减少工作线程因内存回收而导致的停顿，HotSpot虚拟机开发团队在JDK 1.3之后的Java发展历程中研发出了各种其他的优秀收集器，这些将在稍后介绍。但是这些收集器的诞生并不意味着Serial收集器已经“老而无用”，实际上到现在为止，它依然是H**otSpot虚拟机运行在Client模式下的默认的新生代收集器**。它也有着优于其他收集器的地方：**简单而高效（与其他收集器的单线程相比），对于限定单个CPU的环境来说，Serial收集器由于没有线程交互的开销，专心做垃圾收集自然可以获得更高的单线程收集效率。**

在用户的桌面应用场景中，分配给虚拟机管理的内存一般不会很大，收集几十兆甚至一两百兆的新生代（仅仅是新生代使用的内存，桌面应用基本不会再大了），停顿时间完全可以控制在几十毫秒最多一百毫秒以内，只要不频繁发生，这点停顿时间可以接收受。所以，Serial收集器对于运行在Client模式下的虚拟机来说是一个很好的选择。

##### ParNew 收集器

**ParNew**收集器就是Serial收集器的多线程版本，它也是一个**新生代收集器**。除了使用多线程进行垃圾收集外，其余行为包括Serial收集器可用的所有控制参数、收集算法（复制算法）、Stop The World、对象分配规则、回收策略等与Serial收集器完全相同，两者共用了相当多的代码。

ParNew收集器的工作过程如下图（老年代采用Serial Old收集器）：

![img](https://pic.yupoo.com/crowhawk/605f57b5/75122b84.png)

ParNew收集器除了使用多线程收集外，其他与Serial收集器相比并无太多创新之处，但它却是许多运行在Server模式下的虚拟机中首选的新生代收集器，其中有一个与性能无关的重要原因是，**除了Serial收集器外，目前只有它能和CMS收集器（Concurrent Mark Sweep）配合工作**，CMS收集器是JDK 1.5推出的一个具有划时代意义的收集器，具体内容将在稍后进行介绍。

ParNew 收集器在**单CPU的环境**中绝对不会有比Serial收集器有更好的效果，甚至由于存在线程交互的开销，该收集器在通过超线程技术实现的两个CPU的环境中都不能百分之百地保证可以超越。在**多CPU环境**下，随着CPU的数量增加，它对于GC时系统资源的有效利用是很有好处的。它默认开启的收集线程数与CPU的数量相同，在CPU非常多的情况下可使用**-XX:ParallerGCThreads**参数设置。

##### Parallel Scavenge 收集器

**Parallel Scavenge**收集器也是一个**并行**的**多线程新生代**收集器，它也使用**复制算法**。Parallel Scavenge收集器的特点是它的关注点与其他收集器不同，CMS等收集器的关注点是尽可能缩短垃圾收集时用户线程的停顿时间，而Parallel Scavenge收集器的目标是**达到一个可控制的吞吐量（Throughput）**。

**停顿时间越短就越适合需要与用户交互的程序**，良好的响应速度能提升用户体验。而**高吞吐量**则可以高效率地利用CPU时间，尽快完成程序的运算任务，主要适合**在后台运算而不需要太多交互的任务**。

Parallel Scavenge收集器除了会显而易见地提供可以精确控制吞吐量的参数，还提供了一个参数**-XX:+UseAdaptiveSizePolicy**，这是一个开关参数，打开参数后，就不需要手工指定新生代的大小（-Xmn）、Eden和Survivor区的比例（-XX:SurvivorRatio）、晋升老年代对象年龄（-XX:PretenureSizeThreshold）等细节参数了，虚拟机会根据当前系统的运行情况收集性能监控信息，动态调整这些参数以提供最合适的停顿时间或者最大的吞吐量，这种方式称为**GC自适应的调节策略（GC Ergonomics）**。自适应调节策略也是Parallel Scavenge收集器与ParNew收集器的一个重要区别。

另外值得注意的一点是，Parallel Scavenge收集器无法与CMS收集器配合使用，所以在JDK 1.6推出Parallel Old之前，如果新生代选择Parallel Scavenge收集器，老年代只有Serial Old收集器能与之配合使用。

#### 老年代收集器

##### Serial Old收集器

Serial Old 是 Serial收集器的老年代版本，它同样是一个**单线程收集器**，使用**“标记-整理”（Mark-Compact）**算法。

此收集器的主要意义也是在于给Client模式下的虚拟机使用。如果在Server模式下，它还有两大用途：

- 在JDK1.5 以及之前版本（Parallel Old诞生以前）中与Parallel Scavenge收集器搭配使用。
- 作为CMS收集器的后备预案，在并发收集发生**Concurrent Mode Failure**时使用。

它的工作流程与Serial收集器相同，这里再次给出Serial/Serial Old配合使用的工作流程图：

![img](https://pic.yupoo.com/crowhawk/6b90388c/6c281cf0.png) 

##### Parallel Old收集器

Parallel Old收集器是Parallel Scavenge收集器的老年代版本，使用**多线程**和**“标记-整理”**算法。前面已经提到过，这个收集器是在JDK 1.6中才开始提供的，在此之前，如果新生代选择了Parallel Scavenge收集器，老年代除了Serial Old以外别无选择，所以在Parallel Old诞生以后，**“吞吐量优先”收集器**终于有了比较名副其实的应用组合，在**注重吞吐量**以及**CPU资源敏感**的场合，都可以优先考虑Parallel Scavenge加Parallel Old收集器。Parallel Old收集器的工作流程与Parallel Scavenge相同，这里给出Parallel Scavenge/Parallel Old收集器配合使用的流程图：

![img](https://pic.yupoo.com/crowhawk/9a6b1249/b1800d45.png)

##### CMS收集器

**CMS（Concurrent Mark Sweep）**收集器是一种以**获取最短回收停顿时间**为目标的收集器，它非常符合那些集中在互联网站或者B/S系统的服务端上的Java应用，这些应用都非常重视服务的响应速度。从名字上（“Mark Sweep”）就可以看出它是基于**“标记-清除”**算法实现的。

CMS收集器工作的整个流程分为以下4个步骤：

- **初始标记（CMS initial mark）**：仅仅只是标记一下GC Roots能直接关联到的对象，速度很快，需要“Stop The World”。
- **并发标记（CMS concurrent mark）**：进行**GC Roots Tracing**的过程，在整个过程中耗时最长。
- **重新标记（CMS remark）**：为了修正并发标记期间因用户程序继续运作而导致标记产生变动的那一部分对象的标记记录，这个阶段的停顿时间一般会比初始标记阶段稍长一些，但远比并发标记的时间短。此阶段也需要“Stop The World”。
- **并发清除（CMS concurrent sweep）**

由于整个过程中耗时最长的并发标记和并发清除过程收集器线程都可以与用户线程一起工作，所以，从总体上来说，CMS收集器的内存回收过程是与用户线程一起并发执行的。通过下图可以比较清楚地看到CMS收集器的运作步骤中并发和需要停顿的时间：

![img](https://pic.yupoo.com/crowhawk/fffcf9a2/f60599b2.png) 

**优点**

CMS是一款优秀的收集器，它的主要**优点**在名字上已经体现出来了：**并发收集**、**低停顿**，因此CMS收集器也被称为**并发低停顿收集器（Concurrent Low Pause Collector）**。

**缺点**

- **对CPU资源非常敏感** 其实，面向并发设计的程序都对CPU资源比较敏感。在并发阶段，它虽然不会导致用户线程停顿，但会因为占用了一部分线程（或者说CPU资源）而导致应用程序变慢，总吞吐量会降低。**CMS默认启动的回收线程数是（CPU数量+3）/4**，也就是当CPU在4个以上时，并发回收时垃圾收集线程不少于25%的CPU资源，并且随着CPU数量的增加而下降。但是**当CPU不足4个时（比如2个），CMS对用户程序的影响就可能变得很大**，如果本来CPU负载就比较大，还要分出一半的运算能力去执行收集器线程，就可能导致用户程序的执行速度忽然降低了50%，其实也让人无法接受。
- **无法处理浮动垃圾（Floating Garbage）** 可能出现“Concurrent Mode Failure”失败而导致另一次Full GC的产生。**由于CMS并发清理阶段用户线程还在运行着，伴随程序运行自然就还会有新的垃圾不断产生。**这一部分垃圾出现在标记过程之后，CMS无法再当次收集中处理掉它们，只好留待下一次GC时再清理掉。这一部分垃圾就被称为**“浮动垃圾”**。也是由于在垃圾收集阶段用户线程还需要运行，那也就还需要预留有足够的内存空间给用户线程使用，因此CMS收集器不能像其他收集器那样等到老年代几乎完全被填满了再进行收集，需要预留一部分空间提供并发收集时的程序运作使用。
- **标记-清除算法导致的空间碎片** CMS是一款基于“标记-清除”算法实现的收集器，这意味着收集结束时会有大量空间碎片产生。空间碎片过多时，将会给大对象分配带来很大麻烦，往往出现老年代空间剩余，但无法找到足够大连续空间来分配当前对象。

##### G1收集器

**G1（Garbage-First）**收集器是当今收集器技术发展最前沿的成果之一，它是一款**面向服务端应用**的垃圾收集器，HotSpot开发团队赋予它的使命是（在比较长期的）未来可以替换掉JDK 1.5中发布的CMS收集器。与其他GC收集器相比，G1具备如下特点：

- **并行与并发** G1 能充分利用多CPU、多核环境下的硬件优势，使用多个CPU来缩短“Stop The World”停顿时间，部分其他收集器原本需要停顿Java线程执行的GC动作，G1收集器仍然可以通过并发的方式让Java程序继续执行。
- **分代收集** 与其他收集器一样，分代概念在G1中依然得以保留。虽然G1可以不需要其他收集器配合就能独立管理整个GC堆，但它能够采用不同方式去处理新创建的对象和已存活一段时间、熬过多次GC的旧对象来获取更好的收集效果。
- **空间整合** G1从整体来看是基于**“标记-整理”**算法实现的收集器，从局部（两个Region之间）上来看是基于**“复制”**算法实现的。这意味着G1运行期间不会产生内存空间碎片，收集后能提供规整的可用内存。此特性有利于程序长时间运行，分配大对象时不会因为无法找到连续内存空间而提前触发下一次GC。
- **可预测的停顿** 这是G1相对CMS的一大优势，降低停顿时间是G1和CMS共同的关注点，但G1除了降低停顿外，还能建立可预测的停顿时间模型，能让使用者明确指定在一个长度为M毫秒的时间片段内，消耗在GC上的时间不得超过N毫秒，这几乎已经是实时Java（RTSJ）的垃圾收集器的特征了。

**横跨整个堆内存**

在G1之前的其他收集器进行收集的范围都是整个新生代或者老生代，而G1不再是这样。G1在使用时，Java堆的内存布局与其他收集器有很大区别，它**将整个Java堆划分为多个大小相等的独立区域（Region）**，虽然还保留新生代和老年代的概念，但**新生代和老年代不再是物理隔离的了，而都是一部分Region（不需要连续）的集合**。

**建立可预测的时间模型**

G1收集器之所以能建立可预测的停顿时间模型，是因为它可以**有计划地避免在整个Java堆中进行全区域的垃圾收集**。G1跟踪各个Region里面的垃圾堆积的价值大小（回收所获得的空间大小以及回收所需时间的经验值），**在后台维护一个优先列表**，每次根据允许的收集时间，**优先回收价值最大的Region（这也就是Garbage-First名称的来由）**。这种使用Region划分内存空间以及有优先级的区域回收方式，保证了G1收集器在有限的时间内可以获取尽可能高的收集效率。

**避免全堆扫描——Remembered Set**

G1把Java堆分为多个Region，就是“化整为零”。但是Region不可能是孤立的，一个对象分配在某个Region中，可以与整个Java堆任意的对象发生引用关系。在做可达性分析确定对象是否存活的时候，需要扫描整个Java堆才能保证准确性，这显然是对GC效率的极大伤害。

为了避免全堆扫描的发生，虚拟机**为G1中每个Region维护了一个与之对应的Remembered Set**。虚拟机发现程序在对Reference类型的数据进行写操作时，会产生一个Write Barrier暂时中断写操作，检查Reference引用的对象是否处于不同的Region之中（在分代的例子中就是检查是否老年代中的对象引用了新生代中的对象），如果是，便通过CardTable**把相关引用信息记录到被引用对象所属的Region的Remembered Set之中**。当进行内存回收时，在GC根节点的枚举范围中加入Remembered Set即可保证不对全堆扫描也不会有遗漏。

如果不计算维护Remembered Set的操作，G1收集器的运作大致可划分为以下几个步骤：

- **初始标记（Initial Marking）** 仅仅只是标记一下GC Roots 能直接关联到的对象，并且修改**TAMS（Nest Top Mark Start）**的值，让下一阶段用户程序并发运行时，能在正确可以的Region中创建对象，此阶段需要**停顿线程**，但耗时很短。
- **并发标记（Concurrent Marking）** 从GC Root 开始对堆中对象进行**可达性分析**，找到存活对象，此阶段耗时较长，但**可与用户程序并发执行**。
- **最终标记（Final Marking）** 为了修正在并发标记期间因用户程序继续运作而导致标记产生变动的那一部分标记记录，虚拟机将这段时间对象变化记录在**线程的Remembered Set Logs**里面，最终标记阶段需要**把Remembered Set Logs的数据合并到Remembered Set中**，这阶段需要**停顿线程**，但是**可并行执行**。
- **筛选回收（Live Data Counting and Evacuation）** 首先对各个Region中的回收价值和成本进行排序，根据用户所期望的GC 停顿是时间来制定回收计划。此阶段其实也可以做到与用户程序一起并发执行，但是因为只回收一部分Region，时间是用户可控制的，而且停顿用户线程将大幅度提高收集效率。

通过下图可以比较清楚地看到G1收集器的运作步骤中并发和需要停顿的阶段（Safepoint处）：

![img](https://pic.yupoo.com/crowhawk/53b7a589/0bce1667.png)

#### 总结

| 收集器                | 串行、并行or并发 | 新生代/老年代 | 算法               | 目标         | 适用场景                                  |
| --------------------- | ---------------- | ------------- | ------------------ | ------------ | ----------------------------------------- |
| **Serial**            | 串行             | 新生代        | 复制算法           | 响应速度优先 | 单CPU环境下的Client模式                   |
| **Serial Old**        | 串行             | 老年代        | 标记-整理          | 响应速度优先 | 单CPU环境下的Client模式、CMS的后备预案    |
| **ParNew**            | 并行             | 新生代        | 复制算法           | 响应速度优先 | 多CPU环境时在Server模式下与CMS配合        |
| **Parallel Scavenge** | 并行             | 新生代        | 复制算法           | 吞吐量优先   | 在后台运算而不需要太多交互的任务          |
| **Parallel Old**      | 并行             | 老年代        | 标记-整理          | 吞吐量优先   | 在后台运算而不需要太多交互的任务          |
| **CMS**               | 并发             | 老年代        | 标记-清除          | 响应速度优先 | 集中在互联网站或B/S系统服务端上的Java应用 |
| **G1**                | 并发             | both          | 标记-整理+复制算法 | 响应速度优先 | 面向服务端应用，将来替换CMS               |

### 一个Java应用上线后, 关注哪些性能指标. 

[对Java意义重大的7个性能指标](https://blog.csdn.net/hemin1003/article/details/53302890)

#### 1.响应时间和吞吐量

根据应用程序的响应时间可以知道程序完成传输数据所用的时间。也可以从HTTP请求级别，或者成为数据库级别来看。对那些缓慢的查询你需要做一些优化来缩短时间。吞吐量是另一个角度衡量传输数据的指标，是指单位时间内系统处理的客户请求的数量。
我们可以使用APMs（例如New Relic或AppDynamics）来衡量这些指标。使用这些工具，你可以在主报告仪表板中将平均响应时间与昨天的甚至上周的直接进行对比。这有助于我们观察新的部署是否会影响到我们的应用程序。你可以看到网络传输的百分比，测量HTTP完成请求需要多长时间。

推荐工具：
AppDynamics
New Relic
Ruxit

New Relic报告：Web传输百分比和吞吐量

#### 2.平均负载

第二个应用广泛的指标是平均负载。我们习惯上会把平均负载分为这三步测量，分别是第5分钟、第15分钟和最后1分钟。要保证数量低于机器的内核数。一旦超过内核数，机器就会运行在压力状态下。

除了简单测量CPU使用率，还需要关注每个内核的队列中有多少进程。在内核使用率都是100%的情况下，队列中只有1个任务和有6个任务有很大不同。因此，平均负载不能只考虑CPU使用率。

推荐工具：

htop

#### 3.错误率

大多数开发人员判断错误率是根据HTTP传输总失败百分比。但是他们忽略了一个更深层的东西：特定传输的错误率。这直接影响到您应用程序的运行状况。这可以显示出代码方法的错误以及错误或异常出现的次数。

但单纯的错误率数据对我们没有多大帮助。最重要的是我们要找到它们的根源并解决问题。随着Takipi的运行，我们要在日志文件中需找线索。你可以找到所有关于服务器状态的信息，包括堆栈跟踪、源代码和变量值。

推荐工具：
Takipi

#### 4.GC率和暂停时间

异常行为垃圾收集器应用程序的吞吐量和响应时间采取深潜的主要原因之一。了解GC暂停频率和持续时间的关键是分析GC日志文件。要分析它们，你需要收集GC日志和JVM参数。你要注意观察不同指标之间的数据是如何相互影响的。

推荐工具：
jClarity Censum
GCViewer

#### 5.业务指标

应用程序的性能不完全取决于响应时间和错误率。业务指标也是一方面，例如收益、用户数。

推荐工具：
Grafana
The ELK stack
Datadog

Librato

#### 6.正常运行时间和服务运行状态

这一指标奠定了整个应用程序性能的基础。不仅可以当做一个提醒指标，也可以让你定义一段时间内的SKA。我们可以使用Pingdom的servlet功能进行运行状态检查。我们可以查到应用程序的所有传输，包括数据库和S3。

推荐工具：
Pingdom

#### 7.日志大小

日志有一个缺点，它是一直在增加的。当您的服务器启动塞满了垃圾，一切都慢下来。因此，我们需要密切的关注日志大小。

目前通常的解决办法是使用logstash划分使用日志，并将它们发送并存储在Splunk、ELK或其他的日志管理工具中。

推荐工具：
ELK（看看搭建过程）
Splunk
Sumo Logic
Loggly

----

### 如果响应时间过长或者CPU占用过高, 如何排查, 用哪些工具或命令

[使用jstack排查线上故障：高CPU占用](https://zhuanlan.zhihu.com/p/47004229)

#### 排查步骤

> 思路：找出tomcat 进程中使用CPU最高、时间最长的线程，分析堆栈信息，定位问题。

1、 先拿到tomcat进程ID

```
ps –ef | grep tomcat 
```

记录下tomcat应用进程的ID: 30027（我拿到的是这个值）

> 这里按照我们排查故障的思维习惯，是直接怀疑我们部署的tomcat应用服务出现故障。如果你不确定，比如怀疑是同时部署到这台机的监控程序或其他进程引起的问题，那可以先使用top命令查看到底是哪个进程占用CPU过高，再拿这个PID走下面的流程。

2、 拿到CPU占用最高、时间最长的线程ID

```
# 显示进程号为30027的进程信息，CPU、内存占用率等
top -H -p 3002
```

![img](https://pic2.zhimg.com/80/v2-2f1ae947a07ea4022da3d3251dd67bad_hd.jpg)

> 当然这一步你也可以使用以下这个命令，显示特定PID下全部线程列表，以定位到是哪个线程占用CPU时间最高：

```
ps -mp pid -o THREAD,tid,time
```

3、 将需要的线程ID转换为16进制格式

```
printf "%x\n" 1852
```

得到结果：73c（这里我只查看了1852这个线程ID）

4、打印线程的堆栈信息

```
jstack 30027 |grep 73c -A 30
```

得到如下信息：

```
"pool-1617252-thread-1" prio=10 tid=0x00007f76a00e5800 nid=0x73c runnable [0x00007f7658e61000]
   java.lang.Thread.State: RUNNABLE
        at java.util.Random.next(Random.java:139)
        at java.util.Random.nextDouble(Random.java:394)
        at java.lang.Math.random(Math.java:695)
        at com.xxx.xxx.utils.Rand.randomNoSame(Rand.java:100)
        at com.xxx.xxx.videos.GetVideoListService.getUserPullDownVirtualPageResult(GetVideoListService.java:275)
        at com.xxx.xxx.videos.GetVideoListService.getVideoList(GetVideoListService.java:94)
        at sun.reflect.GeneratedMethodAccessor1123.invoke(Unknown Source)
```

找到出现问题的代码了！

现在来分析下具体的代码：Rand.randomNoSame(Rand.java:100) Rand是应用封装的一个获取随机且不重复列表的工具类。randomNoSame函数的代码如下：

![img](https://pic1.zhimg.com/80/v2-941daa026540ce867f578037d46f34e8_hd.jpg)

 问题就出在标红的代码部分。如果第二次生成的随机数和第一次生成的随机数相等，那flag=false，同时break;只是跳出了当前的for循环，而while循环将一直进行下去。问题的根源找到了，至于具体怎么修改就看业务逻辑和需求了。

 总结

最后，总结下排查CPU故障的方法和技巧有哪些：

1. top命令：Linux命令。可以查看实时的CPU使用情况。也可以查看最近一段时间的CPU使用情况。
2. PS命令：Linux命令。强大的进程状态监控命令。可以查看进程以及进程中线程的当前CPU使用情况。属于当前状态的采样数据。
3. jstack：Java提供的命令。可以查看某个进程的当前线程栈运行情况。根据这个命令的输出可以定位某个进程的所有线程的当前运行状态、运行代码，以及是否死锁等等。





---



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

在分析ArrayList的内存之前，有必须先了解下ArrayList的实现原理。
ArrayList实现List接口，底层使用数组保存所有元素。其操作基本上是对数组的操作。下面分析下源代码：

##### 1. 底层使用数组保存数据：

```java
    /**
     * The array buffer into which the elements of the ArrayList are stored.
     * The capacity of the ArrayList is the length of this array buffer. Any
     * empty ArrayList with elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA
     * will be expanded to DEFAULT_CAPACITY when the first element is added.
     */
    transient Object[] elementData; // non-private to simplify nested class access
```

##### 2. 构造方法：

ArrayList提供了三种方式的构造器，可以构造一个默认的空列表、构造一个指定初始容量的空列表及构造一个包含指定collection元素的列表，这些元素按照该collection的迭代器返回它们的顺序排列。

```java
    /**
     * Shared empty array instance used for default sized empty instances. We
     * distinguish this from EMPTY_ELEMENTDATA to know how much to inflate when
     * first element is added.
     */
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
    
    /**
     * Constructs an empty list with the specified initial capacity.
     *
     * @  initialCapacity  the initial capacity of the list
     * @throws IllegalArgumentException if the specified initial capacity
     *         is negative
     */
    public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }

    /**
     * Constructs an empty list with an initial capacity of ten.
     */
    public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }

    /**
     * Constructs a list containing the elements of the specified
     * collection, in the order they are returned by the collection's
     * iterator.
     *
     * @ c the collection whose elements are to be placed into this list
     * @throws NullPointerException if the specified collection is null
     */
    public ArrayList(Collection<? extends E> c) {
        elementData = c.toArray();
        if ((size = elementData.length) != 0) {
            // c.toArray might (incorrectly) not return Object[] (see 6260652)
            if (elementData.getClass() != Object[].class)
                elementData = Arrays.copyOf(elementData, size, Object[].class);
        } else {
            // replace with empty array.
            this.elementData = EMPTY_ELEMENTDATA;
        }
    }
```

##### 3. 存储：

ArrayList提供了set(int index, E element)、add(E e)、add(int index, E element)、addAll(Collection<? extends E> c)等，这里着重介绍一下add(E e)方法。

```java
    /**
     * Appends the specified element to the end of this list.
     *
     * @ e element to be appended to this list
     * @return <tt>true</tt> (as specified by {@link Collection#add})
     */
    public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }
```

add方法将指定的元素添加到此列表的尾部。这里注意下ensureCapacityInternal方法，这个方法会检查添加后元素的个数是否会超过当前数组的长度，如果超出，数组将会进行扩容。 

```java
    /**
     * Default initial capacity.
     */
    private static final int DEFAULT_CAPACITY = 10;

    /**
     * Increases the capacity of this <tt>ArrayList</tt> instance, if
     * necessary, to ensure that it can hold at least the number of elements
     * specified by the minimum capacity argument.
     *
     * @   minCapacity   the desired minimum capacity
     */
    public void ensureCapacity(int minCapacity) {
        int minExpand = (elementData != DEFAULTCAPACITY_EMPTY_ELEMENTDATA)
            // any size if not default element table
            ? 0
            // larger than default for default empty table. It's already
            // supposed to be at default size.
            : DEFAULT_CAPACITY;

        if (minCapacity > minExpand) {
            ensureExplicitCapacity(minCapacity);
        }
    }

    private void ensureCapacityInternal(int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
        }

        ensureExplicitCapacity(minCapacity);
    }

    private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }
```

如果初始时没有指定ArrayList大小，在第一次调用add方法时，会初始化数组默认最小容量为10。看下grow方法的源码：

```java
    /**
     * Increases the capacity to ensure that it can hold at least the
     * number of elements specified by the minimum capacity argument.
     *
     * @ minCapacity the desired minimum capacity
     */
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```

从上述代码可以看出，数组进行扩容时，会将老数组中的元素重新拷贝一份到新的数组中，每次数组扩容的增长是原容量的1.5倍。这种操作的代价是很高的，因此在实际使用时，应该尽量避免数组容量的扩张。当可预知要保存的元素的数量时，要在构造ArrayList实例时，就指定其容量，以避免数组扩容的发生。或者根据实际需求，通过调用ensureCapacity方法来手动增加ArrayList实例的容量。

ArrayList其他操作读取删除等原理这里不作介绍了。

##### 4. 内存占用

下面开始分析ArrayList的内存占用情况。ArrayList继承AbstractList类，AbstractList拥有一个int类型的modCount属性，ArrayList本身拥有一个int类型的size属性和一个数组属性。
所以一个ArrayList实例本身的的大小为：

> 12(header) + 4(modCount) + 4(size) + 4(elementData reference) = 24 (bytes)

下面分析一个只有一个Integer(1)元素的ArrayList<Integer>实例占用的内存大小。

```java
   ArrayList<Integer> testList = Lists.newArrayList();
   testList.add(1);
```

根据上面对ArrayList原理的介绍，当调用add方法时，ArrayList会初始化一个默认大小为10的数组，而数组中保存的Integer(1)实例大小为16 bytes。
则testList占用的内存大小为：

> 24(ArrayList itselft) + 16(elementData array header) + 10 * 4(elemetData reference) + 16(Integer) = 96 (bytes)

JProfiler中的结果验证了上述分析：

![clipboard.png](https://static.segmentfault.com/v-5c4ec07f/global/img/squares.svg)

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

 

 

 

 

 

  

 

 

 

 

 

 

 

 

  

 

 

 

  

 

 

 















 

 

 

 

 



