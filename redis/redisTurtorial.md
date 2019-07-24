# Redis 安装

* 测试是否安装成功

  > ping

* redis 启动

  > redis-cli -h 127.0.0.1 -p 6379

* 设置键值对

  > set myKey abc

* 取出键值对

  > get myKey

# Redis 配置

* 查看配置项

  > config get *    //查看所有 配置项
  >
  > config get loglevel

* 编辑配置

  > config set config_setting_name new_config_value
  >
  > config set loglevel “notice”
  >
  > config get loglevel

* 参数说明

   

  | 序号 | 配置项                                                       | 说明                                                         |
  | ---- | ------------------------------------------------------------ | ------------------------------------------------------------ |
  | 1    | `daemonize no`                                               | Redis 默认不是以守护进程的方式运行，可以通过该配置项修改，使用 yes 启用守护进程（Windows 不支持守护线程的配置为 no ） |
  | 2    | `pidfile /var/run/redis.pid`                                 | 当 Redis 以守护进程方式运行时，Redis 默认会把 pid 写入 /var/run/redis.pid 文件，可以通过 pidfile 指定 |
  | 3    | `port 6379`                                                  | 指定 Redis 监听端口，默认端口为 6379，作者在自己的一篇博文中解释了为什么选用 6379 作为默认端口，因为 6379 在手机按键上 MERZ 对应的号码，而 MERZ 取自意大利歌女 Alessia Merz 的名字 |
  | 4    | `bind 127.0.0.1`                                             | 绑定的主机地址                                               |
  | 5    | `timeout 300`                                                | 当客户端闲置多长时间后关闭连接，如果指定为 0，表示关闭该功能 |
  | 6    | `loglevel notice`                                            | 指定日志记录级别，Redis 总共支持四个级别：debug、verbose、notice、warning，默认为 notice |
  | 7    | `logfile stdout`                                             | 日志记录方式，默认为标准输出，如果配置 Redis 为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给 /dev/null |
  | 8    | `databases 16`                                               | 设置数据库的数量，默认数据库为0，可以使用SELECT 命令在连接上指定数据库id |
  | 9    | `save <seconds> <changes>`Redis 默认配置文件中提供了三个条件：**save 900 1****save 300 10****save 60 10000**分别表示 900 秒（15 分钟）内有 1 个更改，300 秒（5 分钟）内有 10 个更改以及 60 秒内有 10000 个更改。 | 指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合 |
  | 10   | `rdbcompression yes`                                         | 指定存储至本地数据库时是否压缩数据，默认为 yes，Redis 采用 LZF 压缩，如果为了节省 CPU 时间，可以关闭该选项，但会导致数据库文件变的巨大 |
  | 11   | `dbfilename dump.rdb`                                        | 指定本地数据库文件名，默认值为 dump.rdb                      |
  | 12   | `dir ./`                                                     | 指定本地数据库存放目录                                       |
  | 13   | `slaveof <masterip> <masterport>`                            | 设置当本机为 slav 服务时，设置 master 服务的 IP 地址及端口，在 Redis 启动时，它会自动从 master 进行数据同步 |
  | 14   | `masterauth <master-password>`                               | 当 master 服务设置了密码保护时，slav 服务连接 master 的密码  |
  | 15   | `requirepass foobared`                                       | 设置 Redis 连接密码，如果配置了连接密码，客户端在连接 Redis 时需要通过 AUTH <password> 命令提供密码，默认关闭 |
  | 16   | ` maxclients 128`                                            | 设置同一时间最大客户端连接数，默认无限制，Redis 可以同时打开的客户端连接数为 Redis 进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis 会关闭新的连接并向客户端返回 max number of clients reached 错误信息 |
  | 17   | `maxmemory <bytes>`                                          | 指定 Redis 最大内存限制，Redis 在启动时会把数据加载到内存中，达到最大内存后，Redis 会先尝试清除已到期或即将到期的 Key，当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis 新的 vm 机制，会把 Key 存放内存，Value 会存放在 swap 区 |
  | 18   | `appendonly no`                                              | 指定是否在每次更新操作后进行日志记录，Redis 在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis 本身同步数据文件是按上面 save 条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为 no |
  | 19   | `appendfilename appendonly.aof`                              | 指定更新日志文件名，默认为 appendonly.aof                    |
  | 20   | `appendfsync everysec`                                       | 指定更新日志条件，共有 3 个可选值：**no**：表示等操作系统进行数据缓存同步到磁盘（快）**always**：表示每次更新操作后手动调用 fsync() 将数据写到磁盘（慢，安全）**everysec**：表示每秒同步一次（折中，默认值） |
  | 21   | `vm-enabled no`                                              | 指定是否启用虚拟内存机制，默认值为 no，简单的介绍一下，VM 机制将数据分页存放，由 Redis 将访问量较少的页即冷数据 swap 到磁盘上，访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析 Redis 的 VM 机制） |
  | 22   | `vm-swap-file /tmp/redis.swap`                               | 虚拟内存文件路径，默认值为 /tmp/redis.swap，不可多个 Redis 实例共享 |
  | 23   | `vm-max-memory 0`                                            | 将所有大于 vm-max-memory 的数据存入虚拟内存，无论 vm-max-memory 设置多小，所有索引数据都是内存存储的(Redis 的索引数据 就是 keys)，也就是说，当 vm-max-memory 设置为 0 的时候，其实是所有 value 都存在于磁盘。默认值为 0 |
  | 24   | `vm-page-size 32`                                            | Redis swap 文件分成了很多的 page，一个对象可以保存在多个 page 上面，但一个 page 上不能被多个对象共享，vm-page-size 是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page 大小最好设置为 32 或者 64bytes；如果存储很大大对象，则可以使用更大的 page，如果不确定，就使用默认值 |
  | 25   | `vm-pages 134217728`                                         | 设置 swap 文件中的 page 数量，由于页表（一种表示页面空闲或使用的 bitmap）是在放在内存中的，，在磁盘上每 8 个 pages 将消耗 1byte 的内存。 |
  | 26   | `vm-max-threads 4`                                           | 设置访问swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4 |
  | 27   | `glueoutputbuf yes`                                          | 设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启 |
  | 28   | `hash-max-zipmap-entries 64 hash-max-zipmap-value 512`       | 指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法 |
  | 29   | `activerehashing yes`                                        | 指定是否激活重置哈希，默认为开启（后面在介绍 Redis 的哈希算法时具体介绍） |
  | 30   | `include /path/to/local.conf`                                | 指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件 |

# Redis 数据类型

* String

  * string类型的值最大能存储512MB

    > set name “abc”

  * 可以存储任何数据==>图片或序列化对象等

* Hash

  * 一个键值对集合,适合存储对象

    > hmset myhash field "hello" field2 "world"

    > hget myhash field

  * 每个hash可以存储2^32-1键值对(40多亿)

* List

  * 按照插入顺序排序。可以添加一个元素到列表的头部（左边）或者尾部（右边）

    > lpush field redis
    >
    > lpush field mongodb
    >
    > lpush field rabitmq	
    >
    > lrange field 0 10

  * 列表最多可存储2^32-1元素(40多亿)

* Set

  * String类型的无序集合

  * 集合是通过哈希表实现的

  * 删除,查找复杂长O(1)

  * sadd命令

    * 添加一个string元素到key对应的set集合中,成功返回1,如果元素已经在集合中返回0,如果key对应的set不存在则返回错误

    > sadd key member

    * 如果一个value已被插入,第二次插入时该元素将被忽略

  * 集合最大成员数据是2^32-1(40多亿)

* zset

  * 有序集合

  * 每个元素都会关联一个double类型的分数,redis通过分数来为集合中的成员进行从小到大的排序

  * 分数是唯一的,但分数(score)却可以重复

  * zadd命令

    > zadd key score member

    ```
    zadd field 0 redis 
    zadd field 0 mengodb
    ```

# 数据类型应用场景

  

| 类型                 | 简介                                                   | 特性                                                         | 场景                                                         |
| -------------------- | ------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| String(字符串)       | 二进制安全                                             | 可以包含任何数据,比如jpg图片或者序列化的对象,一个键最大能存储512M | ---                                                          |
| Hash(字典)           | 键值对集合,即编程语言中的Map类型                       | 适合存储对象,并且可以像数据库中update一个属性一样只修改某一项属性值(Memcached中需要取出整个字符串反序列化成对象修改完再序列化存回去) | 存储、读取、修改用户属性                                     |
| List(列表)           | 链表(双向链表)                                         | 增删快,提供了操作某一段元素的API                             | 1,最新消息排行等功能(比如朋友圈的时间线) 2,消息队列          |
| Set(集合)            | 哈希表实现,元素不重复                                  | 1、添加、删除,查找的复杂度都是O(1) 2、为集合提供了求交集、并集、差集等操作 | 1、共同好友 2、利用唯一性,统计访问网站的所有独立ip 3、好友推荐时,根据tag求交集,大于某个阈值就可以推荐 |
| Sorted Set(有序集合) | 将Set中的元素增加一个权重参数score,元素按score有序排列 | 数据插入集合时,已经进行天然排序                              | 1、排行榜 2、带权重的消息队列                                |

# Redis 命令

* 连接远程主机

  > redis-cli -h host -p port -a password

* 序号

## Redis 字符串

| 序号 | 命令及描述                                                   |
| ---- | ------------------------------------------------------------ |
| 1    | [SET key value](https://www.runoob.com/redis/strings-set.html)  设置指定 key 的值 |
| 2    | [GET key](https://www.runoob.com/redis/strings-get.html)  获取指定 key 的值。 |
| 3    | [GETRANGE key start end](https://www.runoob.com/redis/strings-getrange.html)  返回 key 中字符串值的子字符 |
| 4    | [GETSET key value](https://www.runoob.com/redis/strings-getset.html) 将给定 key 的值设为 value ，并返回 key 的旧值(old value)。 |
| 5    | [GETBIT key offset](https://www.runoob.com/redis/strings-getbit.html) 对 key 所储存的字符串值，获取指定偏移量上的位(bit)。 |
| 6    | [MGET key1 [key2..\]](https://www.runoob.com/redis/strings-mget.html) 获取所有(一个或多个)给定 key 的值。 |
| 7    | [SETBIT key offset value](https://www.runoob.com/redis/strings-setbit.html) 对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)。 |
| 8    | [SETEX key seconds value](https://www.runoob.com/redis/strings-setex.html) 将值 value 关联到 key ，并将 key 的过期时间设为 seconds (以秒为单位)。 |
| 9    | [SETNX key value](https://www.runoob.com/redis/strings-setnx.html) 只有在 key 不存在时设置 key 的值。 |
| 10   | [SETRANGE key offset value](https://www.runoob.com/redis/strings-setrange.html) 用 value 参数覆写给定 key 所储存的字符串值，从偏移量 offset 开始。 |
| 11   | [STRLEN key](https://www.runoob.com/redis/strings-strlen.html) 返回 key 所储存的字符串值的长度。 |
| 12   | [MSET key value [key value ...\]](https://www.runoob.com/redis/strings-mset.html) 同时设置一个或多个 key-value 对。 |
| 13   | [MSETNX key value [key value ...\]](https://www.runoob.com/redis/strings-msetnx.html)  同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在。 |
| 14   | [PSETEX key milliseconds value](https://www.runoob.com/redis/strings-psetex.html) 这个命令和 SETEX 命令相似，但它以毫秒为单位设置 key 的生存时间，而不是像 SETEX 命令那样，以秒为单位。 |
| 15   | [INCR key](https://www.runoob.com/redis/strings-incr.html) 将 key 中储存的数字值增一。 |
| 16   | [INCRBY key increment](https://www.runoob.com/redis/strings-incrby.html) 将 key 所储存的值加上给定的增量值（increment） 。 |
| 17   | [INCRBYFLOAT key increment](https://www.runoob.com/redis/strings-incrbyfloat.html) 将 key 所储存的值加上给定的浮点增量值（increment） 。 |
| 18   | [DECR key](https://www.runoob.com/redis/strings-decr.html) 将 key 中储存的数字值减一。 |
| 19   | [DECRBY key decrement](https://www.runoob.com/redis/strings-decrby.html) key 所储存的值减去给定的减量值（decrement） 。 |
| 20   | [APPEND key value](https://www.runoob.com/redis/strings-append.html) 如果 key 已经存在并且是一个字符串， APPEND 命令将指定的 value 追加到该 key 原来值（value）的末尾。 |

## Redis 哈希

| 序号 | 命令及描述                                                   |
| ---- | ------------------------------------------------------------ |
| 1    | [HDEL key field1 [field2\]](https://www.runoob.com/redis/hashes-hdel.html)  删除一个或多个哈希表字段 |
| 2    | [HEXISTS key field](https://www.runoob.com/redis/hashes-hexists.html)  查看哈希表 key 中，指定的字段是否存在。 |
| 3    | [HGET key field](https://www.runoob.com/redis/hashes-hget.html)  获取存储在哈希表中指定字段的值。 |
| 4    | [HGETALL key](https://www.runoob.com/redis/hashes-hgetall.html)  获取在哈希表中指定 key 的所有字段和值 |
| 5    | [HINCRBY key field increment](https://www.runoob.com/redis/hashes-hincrby.html)  为哈希表 key 中的指定字段的整数值加上增量 increment 。 |
| 6    | [HINCRBYFLOAT key field increment](https://www.runoob.com/redis/hashes-hincrbyfloat.html)  为哈希表 key 中的指定字段的浮点数值加上增量 increment 。 |
| 7    | [HKEYS key](https://www.runoob.com/redis/hashes-hkeys.html)  获取所有哈希表中的字段 |
| 8    | [HLEN key](https://www.runoob.com/redis/hashes-hlen.html)  获取哈希表中字段的数量 |
| 9    | [HMGET key field1 [field2\]](https://www.runoob.com/redis/hashes-hmget.html)  获取所有给定字段的值 |
| 10   | [HMSET key field1 value1 [field2 value2 \]](https://www.runoob.com/redis/hashes-hmset.html)  同时将多个 field-value (域-值)对设置到哈希表 key 中。 |
| 11   | [HSET key field value](https://www.runoob.com/redis/hashes-hset.html)  将哈希表 key 中的字段 field 的值设为 value 。 |
| 12   | [HSETNX key field value](https://www.runoob.com/redis/hashes-hsetnx.html)  只有在字段 field 不存在时，设置哈希表字段的值。 |
| 13   | [HVALS key](https://www.runoob.com/redis/hashes-hvals.html)  获取哈希表中所有值 |
| 14   | HSCAN key cursor [MATCH pattern] [COUNT count]  迭代哈希表中的键值对。 |

## Redis 列表

| 序号 | 命令及描述                                                   |
| ---- | ------------------------------------------------------------ |
| 1    | [BLPOP key1 [key2 \] timeout](https://www.runoob.com/redis/lists-blpop.html)  移出并获取列表的第一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。 |
| 2    | [BRPOP key1 [key2 \] timeout](https://www.runoob.com/redis/lists-brpop.html)  移出并获取列表的最后一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。 |
| 3    | [BRPOPLPUSH source destination timeout](https://www.runoob.com/redis/lists-brpoplpush.html)  从列表中弹出一个值，将弹出的元素插入到另外一个列表中并返回它； 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。 |
| 4    | [LINDEX key index](https://www.runoob.com/redis/lists-lindex.html)  通过索引获取列表中的元素 |
| 5    | [LINSERT key BEFORE\|AFTER pivot value](https://www.runoob.com/redis/lists-linsert.html)  在列表的元素前或者后插入元素 |
| 6    | [LLEN key](https://www.runoob.com/redis/lists-llen.html)  获取列表长度 |
| 7    | [LPOP key](https://www.runoob.com/redis/lists-lpop.html)  移出并获取列表的第一个元素 |
| 8    | [LPUSH key value1 [value2\]](https://www.runoob.com/redis/lists-lpush.html)  将一个或多个值插入到列表头部 |
| 9    | [LPUSHX key value](https://www.runoob.com/redis/lists-lpushx.html)  将一个值插入到已存在的列表头部 |
| 10   | [LRANGE key start stop](https://www.runoob.com/redis/lists-lrange.html)  获取列表指定范围内的元素 |
| 11   | [LREM key count value](https://www.runoob.com/redis/lists-lrem.html)  移除列表元素 |
| 12   | [LSET key index value](https://www.runoob.com/redis/lists-lset.html)  通过索引设置列表元素的值 |
| 13   | [LTRIM key start stop](https://www.runoob.com/redis/lists-ltrim.html)  对一个列表进行修剪(trim)，就是说，让列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除。 |
| 14   | [RPOP key](https://www.runoob.com/redis/lists-rpop.html)  移除列表的最后一个元素，返回值为移除的元素。 |
| 15   | [RPOPLPUSH source destination](https://www.runoob.com/redis/lists-rpoplpush.html)  移除列表的最后一个元素，并将该元素添加到另一个列表并返回 |
| 16   | [RPUSH key value1 [value2\]](https://www.runoob.com/redis/lists-rpush.html)  在列表中添加一个或多个值 |
| 17   | [RPUSHX key value](https://www.runoob.com/redis/lists-rpushx.html)  为已存在的列表添加值 |

## Redis 集合

| 序号 | 命令及描述                                                   |
| ---- | ------------------------------------------------------------ |
| 1    | [SADD key member1 [member2\]](https://www.runoob.com/redis/sets-sadd.html)  向集合添加一个或多个成员 |
| 2    | [SCARD key](https://www.runoob.com/redis/sets-scard.html)  获取集合的成员数 |
| 3    | [SDIFF key1 [key2\]](https://www.runoob.com/redis/sets-sdiff.html)  返回给定所有集合的差集 |
| 4    | [SDIFFSTORE destination key1 [key2\]](https://www.runoob.com/redis/sets-sdiffstore.html)  返回给定所有集合的差集并存储在 destination 中 |
| 5    | [SINTER key1 [key2\]](https://www.runoob.com/redis/sets-sinter.html)  返回给定所有集合的交集 |
| 6    | [SINTERSTORE destination key1 [key2\]](https://www.runoob.com/redis/sets-sinterstore.html)  返回给定所有集合的交集并存储在 destination 中 |
| 7    | [SISMEMBER key member](https://www.runoob.com/redis/sets-sismember.html)  判断 member 元素是否是集合 key 的成员 |
| 8    | [SMEMBERS key](https://www.runoob.com/redis/sets-smembers.html)  返回集合中的所有成员 |
| 9    | [SMOVE source destination member](https://www.runoob.com/redis/sets-smove.html)  将 member 元素从 source 集合移动到 destination 集合 |
| 10   | [SPOP key](https://www.runoob.com/redis/sets-spop.html)  移除并返回集合中的一个随机元素 |
| 11   | [SRANDMEMBER key [count\]](https://www.runoob.com/redis/sets-srandmember.html)  返回集合中一个或多个随机数 |
| 12   | [SREM key member1 [member2\]](https://www.runoob.com/redis/sets-srem.html)  移除集合中一个或多个成员 |
| 13   | [SUNION key1 [key2\]](https://www.runoob.com/redis/sets-sunion.html)  返回所有给定集合的并集 |
| 14   | [SUNIONSTORE destination key1 [key2\]](https://www.runoob.com/redis/sets-sunionstore.html)  所有给定集合的并集存储在 destination 集合中 |
| 15   | [SSCAN key cursor [MATCH pattern\] [COUNT count]](https://www.runoob.com/redis/sets-sscan.html)  迭代集合中的元素 |

## Redis 有序集合

| 序号 | 命令及描述                                                   |
| ---- | ------------------------------------------------------------ |
| 1    | [ZADD key score1 member1 [score2 member2\]](https://www.runoob.com/redis/sorted-sets-zadd.html)  向有序集合添加一个或多个成员，或者更新已存在成员的分数 |
| 2    | [ZCARD key](https://www.runoob.com/redis/sorted-sets-zcard.html)  获取有序集合的成员数 |
| 3    | [ZCOUNT key min max](https://www.runoob.com/redis/sorted-sets-zcount.html)  计算在有序集合中指定区间分数的成员数 |
| 4    | [ZINCRBY key increment member](https://www.runoob.com/redis/sorted-sets-zincrby.html)  有序集合中对指定成员的分数加上增量 increment |
| 5    | [ZINTERSTORE destination numkeys key [key ...\]](https://www.runoob.com/redis/sorted-sets-zinterstore.html)  计算给定的一个或多个有序集的交集并将结果集存储在新的有序集合 key 中 |
| 6    | [ZLEXCOUNT key min max](https://www.runoob.com/redis/sorted-sets-zlexcount.html)  在有序集合中计算指定字典区间内成员数量 |
| 7    | [ZRANGE key start stop [WITHSCORES\]](https://www.runoob.com/redis/sorted-sets-zrange.html)  通过索引区间返回有序集合成指定区间内的成员 |
| 8    | [ZRANGEBYLEX key min max [LIMIT offset count\]](https://www.runoob.com/redis/sorted-sets-zrangebylex.html)  通过字典区间返回有序集合的成员 |
| 9    | [ZRANGEBYSCORE key min max [WITHSCORES\] [LIMIT]](https://www.runoob.com/redis/sorted-sets-zrangebyscore.html)  通过分数返回有序集合指定区间内的成员 |
| 10   | [ZRANK key member](https://www.runoob.com/redis/sorted-sets-zrank.html)  返回有序集合中指定成员的索引 |
| 11   | [ZREM key member [member ...\]](https://www.runoob.com/redis/sorted-sets-zrem.html)  移除有序集合中的一个或多个成员 |
| 12   | [ZREMRANGEBYLEX key min max](https://www.runoob.com/redis/sorted-sets-zremrangebylex.html)  移除有序集合中给定的字典区间的所有成员 |
| 13   | [ZREMRANGEBYRANK key start stop](https://www.runoob.com/redis/sorted-sets-zremrangebyrank.html)  移除有序集合中给定的排名区间的所有成员 |
| 14   | [ZREMRANGEBYSCORE key min max](https://www.runoob.com/redis/sorted-sets-zremrangebyscore.html)  移除有序集合中给定的分数区间的所有成员 |
| 15   | [ZREVRANGE key start stop [WITHSCORES\]](https://www.runoob.com/redis/sorted-sets-zrevrange.html)  返回有序集中指定区间内的成员，通过索引，分数从高到底 |
| 16   | [ZREVRANGEBYSCORE key max min [WITHSCORES\]](https://www.runoob.com/redis/sorted-sets-zrevrangebyscore.html)  返回有序集中指定分数区间内的成员，分数从高到低排序 |
| 17   | [ZREVRANK key member](https://www.runoob.com/redis/sorted-sets-zrevrank.html)  返回有序集合中指定成员的排名，有序集成员按分数值递减(从大到小)排序 |
| 18   | [ZSCORE key member](https://www.runoob.com/redis/sorted-sets-zscore.html)  返回有序集中，成员的分数值 |
| 19   | [ZUNIONSTORE destination numkeys key [key ...\]](https://www.runoob.com/redis/sorted-sets-zunionstore.html)  计算给定的一个或多个有序集的并集，并存储在新的 key 中 |
| 20   | [ZSCAN key cursor [MATCH pattern\] [COUNT count]](https://www.runoob.com/redis/sorted-sets-zscan.html)  迭代有序集合中的元素（包括元素成员和元素分值） |

## Redis HyperLogLog

Redis 在 2.8.9 版本添加了 HyperLogLog 结构。

* 基数统计的算法
* 在输入元素的数量或者何种非常大时,计算基数所需空间总是固定的,并且很小
  * 什么是基数?
    * 数据集{1,3,5,7,5,7,8},那么这个数据集的基数集为{1,3,5,7,8},基数为5
    * 基数估计就是在误差可接受的范围内,快速计算基数
* 只需12kb内存,可以计算近2^64个不同元素的基数
* 不会存储元素本身,不能像集合那样,返回输入的各个元素

| 序号 | 命令及描述                                                   |
| ---- | ------------------------------------------------------------ |
| 1    | [PFADD key element [element ...\]](https://www.runoob.com/redis/hyperloglog-pfadd.html)  添加指定元素到 HyperLogLog 中。 |
| 2    | [PFCOUNT key [key ...\]](https://www.runoob.com/redis/hyperloglog-pfcount.html)  返回给定 HyperLogLog 的基数估算值。 |
| 3    | [PFMERGE destkey sourcekey [sourcekey ...\]](https://www.runoob.com/redis/hyperloglog-pfmerge.html)  将多个 HyperLogLog 合并为一个 HyperLogLog |

# Redis 发布订阅

* Redis客户端可以订阅任意数量的频道

  ![img](https://www.runoob.com/wp-content/uploads/2014/11/pubsub1.png)

  ​	当有新消息通过publish命令发送给channel1时,这个消息就会被发送给订阅它的三个客户端

  ![img](https://www.runoob.com/wp-content/uploads/2014/11/pubsub2.png)

* 命令

  * 订阅频道名为redisChat

    > subscribe redisChat

  * 发布订阅消息

    > publish redisChat "redis publish..."

  | 序号 | 命令及描述                                                   |
  | ---- | ------------------------------------------------------------ |
  | 1    | [PSUBSCRIBE pattern [pattern ...\]](https://www.runoob.com/redis/pub-sub-psubscribe.html)  订阅一个或多个符合给定模式的频道。 |
  | 2    | [PUBSUB subcommand [argument [argument ...\]]](https://www.runoob.com/redis/pub-sub-pubsub.html)  查看订阅与发布系统状态。 |
  | 3    | [PUBLISH channel message](https://www.runoob.com/redis/pub-sub-publish.html)  将信息发送到指定的频道。 |
  | 4    | [PUNSUBSCRIBE [pattern [pattern ...\]]](https://www.runoob.com/redis/pub-sub-punsubscribe.html)  退订所有给定模式的频道。 |
  | 5    | [SUBSCRIBE channel [channel ...\]](https://www.runoob.com/redis/pub-sub-subscribe.html)  订阅给定的一个或多个频道的信息。 |
  | 6    | [UNSUBSCRIBE [channel [channel ...\]]](https://www.runoob.com/redis/pub-sub-unsubscribe.html)  指退订给定的频道。 |

# Redis 事务

* 以MULTI开始一个事务

* 以EXEC命令触发事务

* 单个Redis命令的执行是原子性

* 事务的执行非原子性

  ```
  It's important to note that even when a command fails, all the other commands in the queue are processed – Redis will not stop the processing of commands.
  ```

  | 序号 | 命令及描述                                                   |
  | ---- | ------------------------------------------------------------ |
  | 1    | [DISCARD](https://www.runoob.com/redis/transactions-discard.html)  取消事务，放弃执行事务块内的所有命令。 |
  | 2    | [EXEC](https://www.runoob.com/redis/transactions-exec.html)  执行所有事务块内的命令。 |
  | 3    | [MULTI](https://www.runoob.com/redis/transactions-multi.html)  标记一个事务块的开始。 |
  | 4    | [UNWATCH](https://www.runoob.com/redis/transactions-unwatch.html)  取消 WATCH 命令对所有 key 的监视。 |
  | 5    | [WATCH key [key ...\]](https://www.runoob.com/redis/transactions-watch.html)  监视一个(或多个) key ，如果在事务执行之前这个(或这些) key 被其他命令所改动，那么事务将被打断。 |

# Redis 脚本

> eval script numkeys key [key ...] arg [arg ...]

| 序号 | 命令及描述                                                   |
| ---- | ------------------------------------------------------------ |
| 1    | [EVAL script numkeys key [key ...\] arg [arg ...]](https://www.runoob.com/redis/scripting-eval.html)  执行 Lua 脚本。 |
| 2    | [EVALSHA sha1 numkeys key [key ...\] arg [arg ...]](https://www.runoob.com/redis/scripting-evalsha.html)  执行 Lua 脚本。 |
| 3    | [SCRIPT EXISTS script [script ...\]](https://www.runoob.com/redis/scripting-script-exists.html)  查看指定的脚本是否已经被保存在缓存当中。 |
| 4    | [SCRIPT FLUSH](https://www.runoob.com/redis/scripting-script-flush.html)  从脚本缓存中移除所有脚本。 |
| 5    | [SCRIPT KILL](https://www.runoob.com/redis/scripting-script-kill.html)  杀死当前正在运行的 Lua 脚本。 |
| 6    | [SCRIPT LOAD script](https://www.runoob.com/redis/scripting-script-load.html)  将脚本 script 添加到脚本缓存中，但并不立即执行这个脚本。 |

# Redis 连接命令

| 序号 | 命令及描述                                                   |
| ---- | ------------------------------------------------------------ |
| 1    | [AUTH password](https://www.runoob.com/redis/connection-auth.html)  验证密码是否正确 |
| 2    | [ECHO message](https://www.runoob.com/redis/connection-echo.html)  打印字符串 |
| 3    | [PING](https://www.runoob.com/redis/connection-ping.html)  查看服务是否运行 |
| 4    | [QUIT](https://www.runoob.com/redis/connection-quit.html)  关闭当前连接 |
| 5    | [SELECT index](https://www.runoob.com/redis/connection-select.html)  切换到指定的数据库 |

# Redis 服务器

* 获取服务器的统计信息

  > info

  | 序号 | 命令及描述                                                   |
  | ---- | ------------------------------------------------------------ |
  | 1    | [BGREWRITEAOF](https://www.runoob.com/redis/server-bgrewriteaof.html)  异步执行一个 AOF（AppendOnly File） 文件重写操作 |
  | 2    | [BGSAVE](https://www.runoob.com/redis/server-bgsave.html)  在后台异步保存当前数据库的数据到磁盘 |
  | 3    | [CLIENT KILL [ip:port\] [ID client-id]](https://www.runoob.com/redis/server-client-kill.html)  关闭客户端连接 |
  | 4    | [CLIENT LIST](https://www.runoob.com/redis/server-client-list.html)  获取连接到服务器的客户端连接列表 |
  | 5    | [CLIENT GETNAME](https://www.runoob.com/redis/server-client-getname.html)  获取连接的名称 |
  | 6    | [CLIENT PAUSE timeout](https://www.runoob.com/redis/server-client-pause.html)  在指定时间内终止运行来自客户端的命令 |
  | 7    | [CLIENT SETNAME connection-name](https://www.runoob.com/redis/server-client-setname.html)  设置当前连接的名称 |
  | 8    | [CLUSTER SLOTS](https://www.runoob.com/redis/server-cluster-slots.html)  获取集群节点的映射数组 |
  | 9    | [COMMAND](https://www.runoob.com/redis/server-command.html)  获取 Redis 命令详情数组 |
  | 10   | [COMMAND COUNT](https://www.runoob.com/redis/server-command-count.html)  获取 Redis 命令总数 |
  | 11   | [COMMAND GETKEYS](https://www.runoob.com/redis/server-command-getkeys.html)  获取给定命令的所有键 |
  | 12   | [TIME](https://www.runoob.com/redis/server-time.html)  返回当前服务器时间 |
  | 13   | [COMMAND INFO command-name [command-name ...\]](https://www.runoob.com/redis/server-command-info.html)  获取指定 Redis 命令描述的数组 |
  | 14   | [CONFIG GET parameter](https://www.runoob.com/redis/server-config-get.html)  获取指定配置参数的值 |
  | 15   | [CONFIG REWRITE](https://www.runoob.com/redis/server-config-rewrite.html)  对启动 Redis 服务器时所指定的 redis.conf 配置文件进行改写 |
  | 16   | [CONFIG SET parameter value](https://www.runoob.com/redis/server-config-set.html)  修改 redis 配置参数，无需重启 |
  | 17   | [CONFIG RESETSTAT](https://www.runoob.com/redis/server-config-resetstat.html)  重置 INFO 命令中的某些统计数据 |
  | 18   | [DBSIZE](https://www.runoob.com/redis/server-dbsize.html)  返回当前数据库的 key 的数量 |
  | 19   | [DEBUG OBJECT key](https://www.runoob.com/redis/server-debug-object.html)  获取 key 的调试信息 |
  | 20   | [DEBUG SEGFAULT](https://www.runoob.com/redis/server-debug-segfault.html)  让 Redis 服务崩溃 |
  | 21   | [FLUSHALL](https://www.runoob.com/redis/server-flushall.html)  删除所有数据库的所有key |
  | 22   | [FLUSHDB](https://www.runoob.com/redis/server-flushdb.html)  删除当前数据库的所有key |
  | 23   | [INFO [section\]](https://www.runoob.com/redis/server-info.html)  获取 Redis 服务器的各种信息和统计数值 |
  | 24   | [LASTSAVE](https://www.runoob.com/redis/server-lastsave.html)  返回最近一次 Redis 成功将数据保存到磁盘上的时间，以 UNIX 时间戳格式表示 |
  | 25   | [MONITOR](https://www.runoob.com/redis/server-monitor.html)  实时打印出 Redis 服务器接收到的命令，调试用 |
  | 26   | [ROLE](https://www.runoob.com/redis/server-role.html)  返回主从实例所属的角色 |
  | 27   | [SAVE](https://www.runoob.com/redis/server-save.html)  同步保存数据到硬盘 |
  | 28   | [SHUTDOWN [NOSAVE\] [SAVE]](https://www.runoob.com/redis/server-shutdown.html)  异步保存数据到硬盘，并关闭服务器 |
  | 29   | [SLAVEOF host port](https://www.runoob.com/redis/server-slaveof.html)  将当前服务器转变为指定服务器的从属服务器(slave server) |
  | 30   | [SLOWLOG subcommand [argument\]](https://www.runoob.com/redis/server-showlog.html)  管理 redis 的慢日志 |
  | 31   | [SYNC](https://www.runoob.com/redis/server-sync.html)  用于复制功能(replication)的内部命令 |

# Redis 数据备份与恢复

* 备份

  >  save

  ​	将在redis安装目录中创建dump.rdb文件

  > bgsave

  ​	将在后台执行备份

* 恢复

  * 将备份文件dump.rdb 移动到redis安装目录并启动服务即可

  * 获取redis目录

    > config get  dir

# Redis 安全

* 查看是否设置了密码验证

  > config get requirepass

* 设置密码

  > config set requirepass 123456

# Redis 性能测试

* 同时执行10000个请求来检测性能

  > redis-benchmark -n 10000 -q

| 序号 | 选项      | 描述                                       | 默认值    |
| ---- | --------- | ------------------------------------------ | --------- |
| 1    | **-h**    | 指定服务器主机名                           | 127.0.0.1 |
| 2    | **-p**    | 指定服务器端口                             | 6379      |
| 3    | **-s**    | 指定服务器 socket                          |           |
| 4    | **-c**    | 指定并发连接数                             | 50        |
| 5    | **-n**    | 指定请求数                                 | 10000     |
| 6    | **-d**    | 以字节的形式指定 SET/GET 值的数据大小      | 2         |
| 7    | **-k**    | 1=keep alive 0=reconnect                   | 1         |
| 8    | **-r**    | SET/GET/INCR 使用随机 key, SADD 使用随机值 |           |
| 9    | **-P**    | 通过管道传输 <numreq> 请求                 | 1         |
| 10   | **-q**    | 强制退出 redis。仅显示 query/sec 值        |           |
| 11   | **--csv** | 以 CSV 格式输出                            |           |
| 12   | **-l**    | 生成循环，永久执行测试                     |           |
| 13   | **-t**    | 仅运行以逗号分隔的测试命令列表。           |           |
| 14   | **-I**    | Idle 模式。仅打开 N 个 idle 连接并等待。   |           |

# Redis 客户端连接

* Redis通过监听一个TCP端口或者Unix socket 的方式来接收来自客户端的连接

* 当一个连接建立后,会执行以下操作

  * 客户湍socket会被设置为非阻塞模式,因为Redis在网络事件处理上采用的是非阻塞多路复用模型
  * 为这个socket设置TCP_NODELAY属性,禁用Nagle算法
  * 创建一个可读的文件事件用于监听这个客户端socket的数据发送

* 最大连接数(默认为10000)

  > config get maxclients

* 设置最大连接数

  > redis-server --maxclients 100000

  | S.N. | 命令               | 描述                                       |
  | ---- | ------------------ | ------------------------------------------ |
  | 1    | **CLIENT LIST**    | 返回连接到 redis 服务的客户端列表          |
  | 2    | **CLIENT SETNAME** | 设置当前连接的名称                         |
  | 3    | **CLIENT GETNAME** | 获取通过 CLIENT SETNAME 命令设置的服务名称 |
  | 4    | **CLIENT PAUSE**   | 挂起客户端连接，指定挂起的时间以毫秒计     |
  | 5    | **CLIENT KILL**    | 关闭客户端连接                             |

# Redis 管道技术

Redis是一和基于客户端-服务端模型以及请求/响应协议的TCP服务.

* 一般步骤: 
  * 客户端向服务端发送一个查询请求,并监听Socket返回,通常是以阻塞模式,等待服务端响应
  * 服务端处理命令,并将结果返回给客户端.
* Redis 管道技术
  * 可以在服务端未响应时,客户端可以继续向服务端发送请求,并最终一次性读取所有服务端响应
  * 提高了redis服务性能,降低往返延时

# Redis 分区

分割数据到多个Redis实例的处理过程,每个实例只保存key的一个子集

* 优势
  * 利用多台计算机内存的和值,允许我们构造更大的数据库
  * 通过多核和多台计算机,允许我们扩展计算能力
  * 通过多台计算机和网络甜酸器,允许我们扩展网络带宽
* 不足
  * 涉及多个key的操作通常不支持
  * 涉及多个key的redis事务不能使用
  * 数据处理较为复杂
  * 增加或删除容量也比较复杂
* 分区类型
  * 范围分区
  * 哈希分区
    * hash函数将key转换为数字
    * 对整数取模