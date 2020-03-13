# Redis与Memcache的区别

* Memchache多线程，Redis单进程单线程
* Redis支持集群
* Redis数据类型丰富

# 为何Redis单线程这么快

* 纯内存操作
* 非阻塞的IO多路复用
* 单线程避免频繁上下文切换

# Redis单进程单线程模型

- 内存采用的是单进程单线程模型的KV数据库，QPS(每秒内查询次数) 10w
- Redis利用队列技术将并发访问变为串行访问，消除了传统数据库串行控制的开销。
- 多线程处理会涉及到锁，而且多线程处理会涉及到线程切换而消耗CPU。因为CPU不是Redis的瓶颈，Redis的瓶颈最有可能是机器内存或者网络带宽。单线程无法发挥多核CPU性能，不过可以通过在单机开多个Redis实例来解决。

# 多路I/O复用模型

* 通过一种机制，一个进程监视多个描述符，一旦某个描述符就绪（一般读或写就绪），能够通知程序进行相应的读写操作

* 利用select,poll,epoll可以同时监察多个流的I/O事件的能力，本质都是同步IO，读写过程阻塞

* 在空闲时，会把当前线程阻塞，当有一个或多个流有I/O事件时，就从阻塞态唤醒，于是程序就会轮询一遍所有的流（epoll是只轮询真正了出了事件的流），且依次顺序处理就绪的流，避免大量无用操作

* 多路

  * 多个网络连接

* 复用
  
* 复用同一个进程
  
* 优势

  * 采用多路复用技术可以让单个线程高效处理多个连接请求，（尽量减少网络IO的时间消耗），且Redis在内存中操作数据的速度非常快，即内存内的操作不会成为影响Redis性能的瓶颈
  * 系统开销小，系统不必创建进程/线程，也不必维护这些线程，大大减小了系统开销

  

# Redis特性

- 速度快
  - 因为数据存在内存中，类似于HashMap，HashMap的优势就是查找和操作的时间复杂度都是O(1)
- 支持丰富数据类型
  - 支持string，list，set，sorted set，hash
- 支持事务
  - 操作都是原子性,因为其是单线程的
- 丰富的特性
  - 可用于缓存，消息，按key设置过期时间，过期后将会自动删除


# Redis支持的数据类型

* string：最基本的数据类型，二进制安全的字符串，最大512M。
* list：按照添加顺序保持顺序的字符串列表。
* set：无序的字符串集合，不存在重复的元素。
* sorted set(zset)：已排序的字符串集合。
* Hash：key-value对的一种集合。

![img](https://pic1.zhimg.com/80/v2-9f60b2278b9cedc0aaae7c22d2a02b34_hd.jpg)

## String实现原理

* redis键值对中的key都是string类型的，redis底层是用C写的，对于String并没有直接使用C的字符数组，而是自己封装了一个sds类型

![img](https://img-blog.csdn.net/20170816140922843?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDkwMDc1NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

* 为何需要新建数据类型？

  * C的字符串api是不安全，因为在使用字符数组后，需要跟踪内存的分配，在使用之前，需要预分配内存空间，否则会有缓冲区溢出，用完一个字符串，需要回收，否则会有内存泄漏
  * 将分配内存的事封装，不让调用方得知-->sds作用
  * 空间预分配，惰性回收
    * 每创建 一个新的String，不按实际大小分配，而是预分配更多内存。当字符串截断时，也不是立刻回收内存，而是减少len值，增加free值。字符串插入时，先看free够不够，不够再分配。这样减少了内存分配回收次数

* SDS与C字符串的区别

  ![img](https://img-blog.csdn.net/20170816141720909)

* SDS好处

  * 获取长度O（1）
  * 杜绝缓冲区溢出
  * 减少了字符串改变造成的空间分配次数

## [zset实现原理-跳跃表](https://segmentfault.com/a/1190000013418471)

* 跳跃表在增删改上与红黑树有相同的性能,是一种特殊的链表,链表的每个节点存了层信息,在查找节点时能跳过这些节点

* 跳跃表最低层(第一层)是一个拥有跳跃表所有节点的普通链表,每次往跳跃表插入链表节点时一定会插入到最低层,至于是否会插入最高层,由抛硬币决定

  ![img](https://segmentfault.com/img/remote/1460000013418478)

## zset为什么不用红黑树

* 优点
  * 实现比红黑树简单,比红黑树更容易扩展
  * 红黑树插入删除时为了平衡高度需要旋转附近节点,高并发时需要锁,跳表不用考虑
  * 一般zset操作是执行zrange之类的操作,取出连续节点,这些操作的缓存命中率比红黑树高
* 缺点
  * 占用更多内存,每个节点大小取决于该节点层数
  * 空间局部性较差导致缓存命中率较低

# 从海量Key里查询某一固定前缀的Key

**使用keys对线上的业务的影响**

  * KEYS pattern

    `keys k1*`

    * 查询所有符合给定模式pattern的key

    * KEYS指令一次性返回所有匹配的key

    * 键的数量过大会造成服务器卡顿

* SCAN cursor

   `scan 0 match k1* count 10  `  

   *  基于游标的迭代器,需要基于上一次的游标延续之前的迭代过程
   *  以0作为粮票开始一次新的迭代,直到命令返回游标0完成一次遍历
   *  不保证每次执行都返回某个给定数量的元素,支持模糊查询
   *  一次返回的数量不可控.只能是大概率符合count参数

# [Redis 怎么实现分布式锁？](https://www.cnblogs.com/zhili/p/redisdistributelock.html)

[教程2](https://juejin.im/post/5b16148a518825136137c8db)

* 分布式锁需要解决的问题
  * 互斥性
  * 安全性
  * 死锁
  * 容错

## 加锁的错误方式1

* `SETNX key value`和`expire key seconds`
  
  * setnx 和expire是2条redis指令,不具备原子性,若程序崩溃,则不会释放锁
  
  ```java
  public static void wrongGetLock1(Jedis jedis, String lockKey, String requestId, int expireTime) {
          Long result = jedis.setnx(lockKey, requestId);
          if (result == 1) {
              // 若在这里程序突然崩溃，则无法设置过期时间，将发生死锁
              jedis.expire(lockKey, expireTime);
          }
      }
  ```

## 加锁的错误方式2

* 客户端自己生成过期时间,强制要求分布式环境下所有客户端的时间必须同步
* 当锁,如果多个客户端同时执行jedis.getSet()方法,虽只有一个客户端是加锁的,但这个客户端的锁的过期时间可能被其他客户端覆盖,不具备加锁和解锁必须是同一客户端的特性
  * **解决上面这段代码的方式就是为每个客户端加锁添加一个唯一标示，已确保加锁和解锁操作是来自同一个客户端。**

``` java
 public static boolean wrongGetLock2(Jedis jedis, String lockKey, int expireTime) {
        long expires = System.currentTimeMillis() + expireTime;
        String expiresStr = String.valueOf(expires);
        // 如果当前锁不存在，返回加锁成功
        if (jedis.setnx(lockKey, expiresStr) == 1) {
            return true;
        }

        // 如果锁存在，获取锁的过期时间
        String currentValueStr = jedis.get(lockKey);
        if (currentValueStr != null && Long.parseLong(currentValueStr) < System.currentTimeMillis()) {
            // 锁已过期，获取上一个锁的过期时间，并设置现在锁的过期时间
            String oldValueStr = jedis.getSet(lockKey, expiresStr);
            if (oldValueStr != null && oldValueStr.equals(currentValueStr)) {
                // 考虑多线程并发的情况，只有一个线程的设置值和当前值相同，它才有权利加锁
                return true;
            }
        }
        // 其他情况，一律返回加锁失败
        return false;
    }
```

## 解锁的错误方式1

* 这种不先判断拥有者而直接解锁的方式，会导致任何客户端都可以随时解锁。即使这把锁不是它上锁的。

* ```java
   public static void wrongReleaseLock1(Jedis jedis, String lockKey) {
          jedis.del(lockKey);
      }
  ```

## 解锁的错误方式2

* 判断和删除不是一个原子性操作。在并发的时候很可能发生解除了别的客户端加的锁。

  * 客户端A加锁，一段时间之后客户端A进行解锁操作时，在执行jedis.del()之前，锁突然过期了，此时客户端B尝试加锁成功，然后客户端A再执行del方法，则客户端A将客户端B的锁给解除了。从而不也不满足加锁和解锁必须是同一个客户端特性。
  * **解决思路就是需要保证GET和DEL操作在一个事务中进行，保证其原子性。**

* ```java
  public static void wrongReleaseLock2(Jedis jedis, String lockKey, String requestId) {
  
          // 判断加锁与解锁是不是同一个客户端
          if (requestId.equals(jedis.get(lockKey))) {
              // 若在此时，这把锁突然不是这个客户端的，则会误解锁
              jedis.del(lockKey);
          }
  
  ```

## 加锁正确方式

```redis
SET key value [EX seconds] [PX milliseconds] [NX|XX]
```

* 参数

  * EX second 

    * 设置键的过期时间为second秒

  * PX millisecond

    * 设置键的过期时间为millisecond 毫秒

  * NX

    * 只在键不存在时,才对键进行设置操作

  * XX

    * 只在键已经存在时,才对键进行设置操作

  * SET操作成功完成时,返回OK,否则返回nil

> set locktarget 12345 ex 10 nx

* 要点

  * value要具有唯一性
  * 释放锁时要验证value值,不能误解锁

* 缺点

  * 加锁时只作用在一个Redis节点上

  * 如果这个master节点发生了主从切换,那么会有锁丢失的情况

    * 在Redis的master节点上拿到了锁,但这个加锁的key还没有同步到slave节点
    * master故障,发生故障转移,slave节点升级为master节点,导致锁丢失

## [分布式锁续期](https://juejin.im/post/5bf3f15851882526a643e207)

如果在公司里落地生产环境用分布式锁的时候，一定是会用开源类库的，比如Redis分布式锁，一般就是用**Redisson**框架就好了，非常的简便易用。

* 默认情况下加锁时间30s，若业务未执行完锁到20s时，会进行一次续期，锁重置为30s
* 若超过30s，客户端还想持有这把锁怎么办
  * 客户端一旦加锁成功。就会启动一个watch dog看门狗，是一个后台线程，每隔10s检查一下，如客户端还持有锁key,就不断延长key生存时间

# [Redis内存回收原理](https://leetcode-cn.com/circle/article/ztZkJJ/)

* 围绕两个方面
  * Redis过期策略
    * 删除过期时间的key值
  * Redis 淘汰策略
    * 内存使用 达到maxmemory上限触发内存淘汰数据
    * 内存淘汰策略用于处理内存不足时的需要申请额外空间的数据.内存淘汰策略的选取并不会影响过期的key的处理,过期策略用于处理过期的缓存数据

## Redis过期策略

* 定时过期
  * 每个设置过期时间的key都要创建一个定时器,到过期时间立即删除,该策略可以立即删除过期的数据,对内存很友好;但会占用大量CPU资源去处理过期数据,从而降低缓存响应时间和吞吐量
* 惰性过期(惰性删除)
  * 当获取某key时，检查是否过期，过期删除且不返回任何东西.对内存不友好,极端情况可能出现大量的过期key没有被再次访问
* 定期过期(定期删除)
  * 每隔一定时间(默认100ms),会扫描一定数量的数据库expires字典中一定数量的key,并其中已过期的key

### 大量的key同时过期的注意事项

* 集中过期, 由于清除大量的key很耗时,会出现短暂的卡顿现象
* 解决
  * 在设置key过期时间的时候,给每个key的**过期时间加上随机值**,使时间分散

## Redis内存淘汰策略

当内存使用达到maxmemory极限时,需要使用LRU淘汰算法来决定清理掉哪些数据,以保证新数据的存入

* volatile-lru 
  * 从**设置过期时间的数据**集中挑选出最近最少使用
* volatile-ttl
  * 从**设置过期时间数据**中选将要过期数据
* volatile-random
  * 从**已设置过期时间数据**中选任意数据
* allkeys-lru
  * 从**所有数据集**中选最近最少使用
* allkeys-random
  * 从**所有数据集**中任意选数据
* noeviction
  * 禁止驱逐数据

# 如何使用Redis做异步队列?

* **使用List作为队列,RPUSH生产消息,LPOP消费消息**
  * 缺点
    * 没有等待队列里有值就直接消费
  * 弥补
    * 可以通过在应用层引用Sleep机制去调用LPOP重试

* `BLPOP key [key ...] timeout` :阻塞直到队列有消息或者超时

  ```redis
  blpop testlist 30   //30秒内一直等待
  ```

  * 缺点
    * 只能供一个消费者消费

* pub/sub:主题订阅者模式

  * 发送者(pub)发送消息,订阅者(sub接收消息)

  * 订阅者可以订阅任意数量的频道(topic)

    * `publish myTopic "Hello"`

    * `subscribe myTopic`

  * 缺点

    * 消息的发布是无状态的,无法保证可达

# Redis如何做持久化

* RDB(快照)持久化
  * 保存某个时间点的全量数据快照
  * SAVE
    * 阻塞Redis的服务进程,直到RDB文件被创建完毕
  * BGSAVE
    * Fork出一个子进程来创建RDB文件,不阻塞服务器进程
  * 缺点
    * 内存数据的全量同步,数据量大会由于I/O而严重影响性能
    * 可能会因为Redis挂掉而丢失从当前至最近一次快照期间的数据
  * 自动触发RDB持久化的方式
    * 根据redis.conf配置里的SAVE m n 定时触发(用的是BGSAVE)
    * 主从复制时,主节点自动触发
    * 执行Debug Reload
    * 执行Shutdown且没有开启AOF
* AOF(Append-Only-File)持久化:保存写状态
  * 记录下除了查询以外的所有变更数据库状态的指令
  * 以append的形式追加保存到AOF文件(增量)
* RDB-AOF混合持久化方式
  * BGSAVE做镜像全量持久化,AOF做增量持久化

## 日志重写解决AOF文件大小不断增大的问题

* AOF重写机制
  * 调用fork(),创建一个子进程(重写子进程)
  * 子进程读取现有AOF文件,将指令分析压缩并写入临时文件中
  * 主进程持续将新的变动同时写到内存和原来的AOF里,以保证原有AOF可用性
  * 主进程获取子进程重写AOF的完成信号,往新AOF同步增量变动
  * 使用新的AOF文件替换掉旧的AOF文件



# [Redis一致性哈希算法](https://zhuanlan.zhihu.com/p/34985026)

## 为什么要对redis集群使用hash

* 假设有一个社交网站,需要用Redis存储图片资源,存储格式为键值对,key为图片名称,value为该图片所在文件服务器的路径,我们需要根据文件名查找路径.由于规则是随机的,我们不确定图片在哪个服务器上,使用Hash比较好

## 为何要有一致性Hash算法

* 在传统的哈希表中，添加或删除一个槽位，几乎需要对所有关键字进行重新映射(增加删除服务器时,需重新hash)
  * 算法使用节点数取余，强依赖node数，当node数发生变化时，数据需要迁移
* 一致性Hash将整个hash值空间组织成一个虚拟的圆环（2^32）
* 将服务器使用Hash（ip或主机名为关键字）进行哈希，确定在环上的位置
* 将数据key使用相同的函数hash计算出哈希值，并确定此数据在环上的位置，从此位置顺时针走，遇到第一台服务器，就是其位置.如果增加一台服务器,则受影响的是新服务器到该环的前一台服务器,需重新定位之间的一小部分服务器位置

## 一致性Hash算法缺点及解决方案

* 不足
  
  * 在服务结点太少时，容易因节点分布不均而造成数据倾斜,大量数据会集中在长度较大的环上
  
* 解决
  
  ​	![img](https://pic3.zhimg.com/80/v2-0368841e5020dd07f1e67f449b49a1ba_720w.jpg)
  
  * 引入虚节点，对每一个服务节点计算多个哈希，每个计算结果位置都放置一个此服务节点，称为虚拟节点
  * 数据定位算法不变，只是多了虚节点到实际结点的映射,以达到数据平均分配的目的
  * 在实际应用中，通常将虚节点数设置为32甚至更大

# Redis数据的恢复

RDB和AOF文件共存情况下的恢复流程

* 检查是否有AOF文件
  * 若存在,则使用AOF文件
  * 否则,使用RDB文件

RDB和AOF的优缺点

* RDB优点
  * 全量数据快照,文件小,恢复快
* RDB缺点
  * 无法保存最近一次快照之后的数据
* AOF优点
  * 可读性高,适合保存增量数据,数据不易丢失
* AOF缺点
  * 文件体积大,恢复时间长

# [Redis的主从同步机制]([https://github.com/AobingJava/JavaFamily/blob/master/docs/redis/%E9%9B%86%E7%BE%A4%E9%AB%98%E5%8F%AF%E7%94%A8%E3%80%81%E5%93%A8%E5%85%B5%E3%80%81%E6%8C%81%E4%B9%85%E5%8C%96%E3%80%81LRU.md](https://github.com/AobingJava/JavaFamily/blob/master/docs/redis/集群高可用、哨兵、持久化、LRU.md))

master机器去写，数据同步给别的slave机器，他们都拿去读，分发掉大量的请求那是不是好很多，而且扩容的时候还可以轻松实现水平扩容。

* 全同步过程
  * Slave发送sync命令到Master
  * Master启动一个后台进程,将Redis中的数据快照保存到文件中
  * Master将保存数据快照期间接收到的写命令缓存起来
  * Master完成写文件操作后,将该文件发送给Slave
  * 使用新的AOF文件替换旧的AOF文件
  * Master 将这期间收集的增量写命令发送给Slave端
* 增量同步过程
  * Master接收到用户的操作指令,判断是否需要传播到Slave
  * 将操作记录追加到AOF文件
  * 将操作传播到其他Slave
    * 对齐主从库
    * 往响应缓存写入指令
  * 将缓存中的数据发送给Slave

# [缓存引发的问题](https://blog.csdn.net/zeb_perfect/article/details/54135506)

## 缓存雪崩

* 设置缓存时采用了相同的过期时间,导致缓存在某一时刻同时失效,请求全部转发到DB,DB瞬时压力过大雪崩
* 解决
  * 加锁或队列的方式保证缓存的单线程写,从而避免失效时大量的并发请求落到底层存储系统上
  * 在原有失效时间上加一个随机值,比如1-5分钟随机,这样缓存时间重复率就会降低,很难引发集体失效事件

## 缓存击穿

* 对一些设置了过期时间的key,如果这些key(热点数据)可能会在某些时间被超高并发地访问,与缓存雪崩的区别是这里针对某一key,前者是很多key
* 缓存在某个时间点过期的时候，恰好在这个时间点对这个Key有大量的并发请求过来，这些请求发现缓存过期一般都会从后端DB加载数据并回设到缓存，这个时候大并发的请求可能会瞬间把后端DB压垮。
* 解决
  * 使用互斥锁(mutex key)
    * 在缓存失效的时候（判断拿出来的值为空），不是立即去load db，而是先使用缓存工具的某些带成功操作返回值的操作（比如Redis的SETNX或者Memcache的ADD）**去set一个mutex key，当操作返回成功时，再进行load db的操作并回设缓存**；否则，就重试整个get缓存的方法。
    * 

## 缓存穿透

* 查询一个一定不存在的数据,**由于缓存是不命中时被动写的**，并且出于容错考虑，如果从存储层查不到数据则不写入缓存，这将导致这个不存在的数据每次请求都要到存储层去查询，失去了缓存的意义。在流量大时，可能DB就挂掉了，要是有人利用不存在的key频繁攻击我们的应用，这就是漏洞。
* 解决
  * 如果一个查询返回的数据为空（不管是数 据不存在，还是系统故障），我们仍然把这个空结果进行缓存，但它的过期时间会很短，最长不超过五分钟。
  * 使用**布隆过滤器**

### [布隆过滤器(Bloom Filter)](https://juejin.im/post/5db69365518825645656c0de)

* 原理
  * 当一个元素被加入集合时，**通过K个散列函数**将这个元素映射成一个位数组中的K个点，把它们置为1。

  * 检索时，我们只要看看这些点是不是都是1就（大约）知道集合中有没有它了：

    * 如果这些点有任何一个0，则被检元素一定不在；如果都是1，则被检元素很可能在。这就是布隆过滤器的基本思想。

    ![img](https://user-gold-cdn.xitu.io/2019/10/28/16e112fbd031fe71?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

* 缺点

  * bloom filter之所以能做到在时间和空间上的效率比较高，是因为牺牲了判断的准确率、删除的便利性
    * 存在误判，可能要查到的元素并没有在容器中，但是hash之后得到的k个位置上值都是1。如果bloom filter中存储的是黑名单，那么可以通过建立一个白名单来存储可能会误判的元素。
    * 删除困难。一个放入容器的元素映射到bit数组的k个位置上是1，删除的时候不能简单的直接置为0，可能会影响其他元素的判断。可以采用[Counting Bloom Filter](http://wiki.corp.qunar.com/confluence/download/attachments/199003276/US9740797.pdf?version=1&modificationDate=1526538500000&api=v2)

# [缓存和数据库一致性](https://juejin.im/post/5c96fb795188252d5f0fdff2)

* **采用延时双删策略**

  * 在写库前后都进行redis.del(key)操作，并且设定合理的超时时间。(只能保证最终一致性,无法保证实时一致性)

    ```java
    public void write( String key, Object data )
    {
        //先删除缓存
    	redis.delKey( key );
        //再写数据库
    	db.updateData( data );
        //休眠500毫秒
    	Thread.sleep( 500 );
        //再次删除缓存
    	redis.delKey( key );
    }
    ```

* **异步更新缓存(基于订阅binlog的同步机制)**

  * **读取binlog后分析 ，利用消息队列,推送更新各台的redis缓存数据。**
  * 一旦MySQL中产生了新的写入、更新、删除等操作，就可以把binlog相关的消息推送至Redis，Redis再根据binlog中的记录，对Redis进行更新。

# Redis集群高可用

* 哨兵集群**sentinel**,哨兵必须用三个实例去保证自己的健壮性的，哨兵+主从并**不能保证数据不丢失**，但是可以保证集群的**高可用**

  ![img](https://camo.githubusercontent.com/15f2ac7d26b5fe90069a405529a099ba672bb753/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f30303679386d4e366c793167387039676a337179616a333039333039707765692e6a7067)

* 哨兵组件的主要功能：

  * 集群监控：负责监控 Redis master 和 slave 进程是否正常工作。
  * 消息通知：如果某个 Redis 实例有故障，那么哨兵负责发送消息作为报警通知给管理员。
  * 故障转移：如果 master node 挂掉了，会自动转移到 slave node 上。
  * 配置中心：如果故障转移发生了，通知 client 客户端新的 master 地址。



