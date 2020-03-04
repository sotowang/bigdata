[TOC]

# Collection

## List

### ArrayList

* 实现List接口，底层使用数组保存所有元素，操作基本上是对数组的操作

  ```java
  transient Object[] elementData; 
  ```

* ArrayList三种构造器

  * 默认空列表，初始容量为10
    * 若没指定ArrayList大小，第一次调用add时，会初始化数组容量为10
  * 指定初始容量的空列表
  * 指定collection元素的列表，按照collection迭代器返回它们的顺序排列

* 存储

  * `add(E e)`
    * 将指定元素添加至列表尾
    * `ensureCapacityInternal()`会检查添加后元素个数是否会超过当前数组长度，若超出则扩容
  * `add(int index,E element)`
  * `addAll(Collection<? extends E> c)`

* 扩容

  * 将老数组无数拷贝进新数组，容量为原容量的1.5倍，实际使用应避免数组扩张
  * 当可预知保存元素数量时，在构造`ArrayList`时就指定容量，以避免扩容，或根据实际需求，通过调用`ensureCapacity()`手动增加容量

* 线程不安全

  * 故障现象`java.util.ConcurrentModificationException`并发修改异常
  * 导致原因
    * 并发修改导致，一个人在写时，另一个人夺走，导致数据不一致异常
  * 解决方案
    * 使用`Vector`
    * `Collections.synchronizedList(new ArrayList<>());`
    * 写时复制`new CopyOnWriteArrayList<>();`
      * 往一个 容器添加元素时不直接在容器添加，而是将当前容器Object[]进行copy复制出一个新容器`Object[] newElements`,然后在新容器中添加元素，添加完成后，将原容器引用指向新数组`setArray(newElements)`
      * 内部使用`ReentrantLock`加锁
      * 好处是可对容器进行并发读而不需加锁，读写分离的思想

#### Array与ArrayList的区别

* Array可包含基本类型和对象类型，ArrayList只能包含对象类型
* Array大小固定，ArrayList大小动态变化
* Array存储同一类型元素

#### 什么时候使用Array而不是ArrayList

* 程序运行期间存在且不变用Array
* 频繁对元素查找，用ArrayList

### LinkedList

### Vector

* 实现与ArrayList类似,但使用了Synchronized同步
* 最好使用ArrayList,因同步操作完全可由程序员控制
* Vector扩容每次扩大2倍(可通过构造函数设置增长量),而ArrayList是1.5倍

#### ArrayList与Vector区别

* 都基于索引和数组，都根据插入顺序获取元素
* 迭代器都是fail-fast，都允许null值，使用索引对元素随机访问
* Vector线程安全，同步，速度比ArrayList慢

## Set

>  元素唯一

### HashSet

* 线程不安全
  * 故障现象
  * 导致原因
  * 解决方案
    * `Collections.synchronizedSet(new HashSet<>());`
    * `new CopyOnWriteArraySet<>();`
* 底层使用HashMap实现
  * `hashSet.add(e)`e作为HashMap的Key，Value全为`PRESENT`的常量，恒定

### TreeSet

* 保证元素排序

## Queue

### PriorityQueue

> 基于优先级堆的无界队列，按照自然顺序排序
>
> 不允许null，因null无序，非线程安全，时间复杂度O(log(n))

* 最大堆
  * 获取数组中最小的几个数
* 最小堆
  * 获取数组中最大的几个数

### BlockingQueue

> 当阻塞队列为空时，从队列获取元素时操作被阻塞
>
> 当队列满时，添加元素操作被阻塞

| 方法类型 | 抛出异常   | 特殊值     | 阻塞     | 超时                 |
| -------- | ---------- | ---------- | -------- | -------------------- |
| 插入     | `add(e)`   | `offer(e)` | `put(e)` | `offer(e,time,unit)` |
| 移除     | `remove()` | `poll()`   | `take()` | `poll(time,unit)`    |
| 检查     | element()  | `peek()`   | 不可用   | 不可用               |

- ArrayBlockingQueue
  - 一个由数组结构组成的有界阻塞队列
- LinkedBlockingQueue
  - 一个由链表结构组成的有界(Integer.MAX_VALUE)/无界阻塞队列
- SynchronousQueue
  - 一个不存储元素的阻塞队列，也即单个元素的队列,产生一个消费一个

- PriorityBlockingQueue
  - 一个支持优先级排序的无界阻塞队列
- DelayQueue
  - 一个使用优先级队列组成的延迟无界阻塞队列
- LinkedTransferQueue
  - 一个由链表结构级成的无界阻塞队列
- LinkedBlockingDeque
  - 一个由链表结构组成的**双向**阻塞队列

# Map

## HashMap

* 底层为数组+链表，java1.7和java1.8表现稍有不同

![img](https://i.loli.net/2019/05/08/5cd1d2be77958.jpg)

* 初始桶大小16，负载因子0.75，当数量达到`16x0.75=12`时扩容，扩容涉及到rehash，复制数据等操作，消耗性能，提前预估HashMap大小最好，减少扩容带来的性能损耗

* 缺点
   *  Hash冲突严重时，链表变长，查找效率降低
   *  解决
      *  java1.8中使用数组+链表+红黑树
      *  ConcurrentHashMap采用分段锁技术，其中Segment继承于`ReentrantLock`，不会像HashTable `put` `get`操作都需加锁，支持Segment数组数量的线程并发，当一线程占用锁Segment时，不影响其它Segment

* 扩容
   
   * 触发时机
   
*  Entry数量>=`threshold=loadFactor*capacity`时，且新建Entry刚好落在一个非空桶上
   
 * 扩容为原来2倍，Java8中不需要重新计算hash，看原来hash值新增的那个bit是1一还是0，是0时索引没变，是1时索引变成`原索引+oldCap`
   
 * 旧桶中链表通过头插法插入新桶中，原链表中的Entry结点并不一定仍在新桶数组同一链表
   
 * HashMap容量为2的幂
   
    * 通过键的Hash值定位桶时，调用`indexFor(hash,table.length)`方法
     
         ```java
         //jdk7
         static int indexFor(int h, int length) {
             return h & (length-1);  //与操作得出对应的桶的位置，&运算比%/运算快10倍，会提高性能，只有length是一个2的幂娄时，h&(length-1)和h%length结果一致
      }
     //jdk8  通过hashCode()的高16位异或低16位实现的
     static final int hash(Object key) {
             int h;
             return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
     }
     ```
   
* put方法的逻辑
  * 如果HashMap未被初始化过，则初始化
  * 对key求Hash值，然后再计算下标
  * 如果没有碰撞，直接放入桶中，如果碰撞，以链表形式插入后面(尾插)
  * 链表长度=8，就把链表转成红黑树;链表长度低于6，就把红黑树转回链表
  * 节点已经存在就替换旧值
  * 如果桶满了（容量16*负载因子0.75），就需要resize（扩容2倍后重排）

* 线程不安全

   * 扩容时会形成环形链表，造成死循环
   * 解决方案
      * `Collections.synchronizedMap()`
      * `ConcurrentHashMap`

### put方法是尾插还是头插

* Java7是头插,Java8是尾插
* 尾插解决了在resize时出现的死循环的问题
* 头插考虑到了一个热点数据的问题,但旧链表迁移新链表时,如果新表数组索引位置相同,则将会链表倒置,使用头插的原因也便不存在了

### HashMap中key可以为任意对象或数据类型么

* 可以为null，不能为可变对象，会导致对象属性改变，hashCode也会改变，查不到已在Map中数据

### 为何容量必须为2的幂

* HashMap是取模操作为`hash & (length-1)`(jdk7)
  * 当length为`2^n`时,对应的二进制为`10000...`,减1后为`1111..`,以时hash为32位二进制int数,按位与(&)时,能快速拿到数组下标,且分布均匀

## HashTable

### HashTable与HashMap的区别

* HashMap不是线程安全的;HashTable是线程安全,使用synchronized同步 
* HashMap允许空（null）的键和值（key），HashTable则不允许。
* HashMap性能优于Hashtable
* Hashtable默认初始容量为11，增加方式为`old*2+1`;HashMap是为16，为2的指数
* HashTable基于Dictionary类，而HashMap基于AbstractMap类

## ConcurrentHashMap

* 特性
  * Java7 使用segment分段锁,继承自`ReentrantLock`
  * java8 CAS+synchronized使锁更细化,CAS操作失败时使用synchronized
  * 数组+链表+红黑树

* put方法的逻辑
  * 如果key或者value为null，则抛出空指针异常； 
  * 如果table为null或者table的长度为0，则初始化table，调用initTable()方法。
  * 通过hash定位数组索引坐标，是否有Node节点，如果没有，使用CAS进行添加（链表的头节点，添加失败则进入下次循环）
  * 如果当前位置的节点元素的hash值为-1，则是一个ForwaringNodes节点，即正在进行扩容。当前线程加入扩容。 
  * 如果f!=null，则使用synchronized锁住f元素（链表/红黑二叉树的头元素）
    * 如果是Node（链表结构），则执行链表的添加操作
    * 如果是TreeNode（树型结构）则执行树添加操作
  * 链表长度达到8时转换为红黑树
* get方法的逻辑
  - 该方法没有同步锁。get方法就是从Hash表中读取数据，而且与扩容不冲突。 
  * 通过key的hash计算索引位置，如果满足条件，直接返回对应的值； 
  * 若相应节点的hash值小于0 ，即该节点在进行扩容，直接在调用ForwardingNodes节点的find方法进行查找。 
  * 否则，遍历当前节点直到找到对应的元素。
* 总结
  * 首先使用无锁操作CAS插入头节点，失败则循环重试
  * 若头节点已存在，则尝试获取头节点的同步锁，再进行操作

## TreeMap

* 传入一个比较器的构造函数，Map中元素按此比较器排序

  `public TreeMap(Comparator<? super K> comparator)`

* 排序逻辑在`Comparator`中实现

## LinkedHashMap

* 继承自HashMap

* 内部维护一个双链表,用来维护插入顺序或者LRU顺序
  * `final boolean accessOrder;`
    * 默认为false,维护插入顺序
    * true时将最近访问节点放入尾部

* LRU缓存

  ```java
  class LRULinkedHashMap<K,V> extends LinkedHashMap<K,V>{
  	//定义缓存的容量
  	private int capacity;
  	private static final long serialVersionUID = 1L;
  	//带参数的构造器	
  	LRULinkedHashMap(int capacity){
  		//调用LinkedHashMap的构造器，传入以下参数
  		super(16,0.75f,true);
  		//传入指定的缓存最大容量
  		this.capacity=capacity;
  	}
  	//实现LRU的关键方法，如果map里面的元素个数大于了缓存最大容量，则删除链表的顶端元素
  	@Override
  	public boolean removeEldestEntry(Map.Entry<K, V> eldest){ 
  		System.out.println(eldest.getKey() + "=" + eldest.getValue());  
  		return size()>capacity;
  	}  
  
  ```


### LinkedHashMap与HashMap区别

* LinkedHashMap是HashMap子类
* LinkedHashMap中Entry有两个指针before和after,用于维护双向链接列表
* LinkedHashMap中向哈希表中插入新Entry时，会通过Entry的addBefore() 将其链入双向链表中
* LinkedHashMap对rehash过程（`transfer`）进行重写
* LinkedHashMap重写了HashMap的get方法，通过HashMap中的getEntry()获取Entry对象，在此基础上进一步获取指定键对应的值



## WeakHashMap

- 其Entry继承自WeakReference,主要用来实现缓存
- 当key为null时，发生GC后将被回收

## HashMap、HashTable、ConcurrentHashMap的区别

* HashMap线程不安全，数组+链表+红黑树
* HashTable线程案例。锁住整个对象，数组+链表
* ConcurrentHashMap线程安全，CAS+同步锁，数组+链表+红黑树
* HashMap的key和value可以为null，其他两个不行



# Collections工具类

## `Collections.sort()`内部原理

* 对于基础类型按照字符表，数字大小排列；对于自定义类型，通过实现Comparable接口，重写Comparator外比较器
* 内部调用`Arrays.sort()`。对于Arrays类，`sort(Object)`使用归并排序，`sort(int)`使用快排

## Iterator与ListIterator区别

- Iterator在Set和List中都有定义。ListIterator仅存在于List接口中
- ListIterator有`add()`方法，可以向List中添加对象，而Iterator不能
- ListIterator可以逆向遍历，可以定位当前的索引位置
- ListIterator可以实现对对象的修改

## 快速失败(fail-fast)与安全失败(fail-safe)的区别

* 快速失败原因
  * 迭代器遍历时访问集合中内容，并使用modCount变量，集合在遍历期间内容改变会改变modCount值，此时会抛出异常
* 安全失败原因
  * 记历时是先复制原集合内容，在拷贝集合上遍历，在遍历过程中对原集合修改不能被迭代器检测，故不会抛异常
* `java.util`下所有集合类都是快速失败；`java.util.concurrent`下所有类为安全失败
* 快速失败会抛出`ConcurrentModificationException`

## Enumeration接口和Iterator接口区别

* Enumeration接口速度是Iterator的2倍，占用更少内存
* Iterator更安全，因其他线程不能修改正在被iterator遍历对象（失败机制）
* Iterator允许调用者删除集合中元素

## 确保一个集合不会被修改

- 使用集合中具，在里面再添加数据时将会报错
  - `map = Collections.unmodifiableMap(map);`
  - `list = Collections.unmodifiableList(list);`



