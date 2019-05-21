# HashMap

HashMap 底层是基于 **`数组 + 链表`** 组成的，不过在 jdk1.7 和 1.8 中具体实现稍有不同。

![img](https://i.loli.net/2019/05/08/5cd1d2be77958.jpg)

 HashMap 中比较核心的几个成员变量；

1. 初始化桶大小，因为底层是数组，所以这是数组默认的大小。
2. 桶最大值。
3. 默认的负载因子（0.75）
4. `table` 真正存放数据的数组。
5. `Map` 存放数量的大小。
6. 桶大小，可在初始化时显式指定。
7. 负载因子，可在初始化时显式指定。

给定的默认容量为 16，负载因子为 0.75。Map 在使用过程中不断的往里面存放数据，当数量达到了 `16 * 0.75 = 12` 就需要将当前 16 的容量进行扩容，而**扩容这个过程涉及到 rehash、复制数据等操作，所以非常消耗性能**。

因此通常建议能提前预估 HashMap 的大小最好，尽量的减少扩容带来的性能损耗。

## 1.7 HashMap缺点

> 当 Hash 冲突严重时，在桶上形成的链表会变的越来越长，这样在查询时的效率就会越来越低；时间复杂度为 `O(N)`。

因此 1.8 中重点优化了这个查询效率。

##  1.8 HashMap结构

![img](https://i.loli.net/2019/05/08/5cd1d2c1c1cd7.jpg)

原理上来说：ConcurrentHashMap 采用了分段锁技术，其中 Segment 继承于 ReentrantLock。不会像 HashTable 那样不管是 put 还是 get 操作都需要做同步处理，理论上 ConcurrentHashMap 支持 CurrencyLevel (Segment 数组数量)的线程并发。每当一个线程占用锁访问一个 Segment 时，不会影响到其他的 Segment。

### HashMap的扩容

**当map中包含的Entry的数量大于等于threshold = loadFactor \* capacity的时候，且新建的Entry刚好落在一个非空的桶上，此刻触发扩容机制，将其容量扩大为2倍。**

**旧桶数组中的某个桶的外挂单链表是通过头插法插入新桶数组中的，并且原链表中的Entry结点并不一定仍然在新桶数组的同一链表**。

**HashMap的容量必须是2的幂**，那么为什么要这么设计呢？答案当然是为了性能。在HashMap通过键的哈希值进行定位桶位置的时候，调用了一个indexFor(hash, table.length);方法。

```java
/**
 * Returns index for hash code h.
 */
static int indexFor(int h, int length) {
    return h & (length-1);
}
```
可以看到这里是将哈希值h与桶数组的length-1（实际上也是map的容量-1）进行了一个与操作得出了对应的桶的位置，h & (length-1)。

但是为什么不采用h % length这种计算方式呢？

https://bugs.java.com/bugdatabase/view_bug.do?bug_id=4631373中提出Java的%、/操作比&慢10倍左右，因此**采用&运算会提高性能**。

通过限制length是一个2的幂数，h & (length-1)和h % length结果是一致的。这就是为什么要限制容量必须是一个2的幂的原因



# HashSet

对于 HashSet 而言，它是基于 HashMap 实现的，底层采用 HashMap 来保存元素

由于 HashMap 的 put() 方法添加 key-value 对时，当新放入 HashMap 的 Entry 中 key 与集合中原有 Entry 的 key 相同（hashCode()返回值相等，通过 equals 比较也返回 true），新添加的 Entry 的 value 会将覆盖原来 Entry 的 value（HashSet 中的 value 都是`PRESENT`），但 key 不会有任何改变，因此**如果向 HashSet 中添加一个已经存在的元素时，新添加的集合元素将不会被放入 HashMap中，原来的元素也不会有任何改变，这也就满足了 Set 中元素不重复的特性。**







