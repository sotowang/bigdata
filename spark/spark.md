* ### spark编译网址
>https://spark.apache.org/docs/2.1.0/building-spark.html
* #### build/mvn
```
./build/mvn -Pyarn -Phadoop-2.6 -Dhadoop.version=2.6.0 -DskipTests clean package
```
* #### Building a Runnable Distribution
```
./dev/make-distribution.sh --name custom-spark --tgz -Psparkr -Phadoop-2.6 -Phive -Phive-thriftserver -Pmesos -Pyarn
```
* ####  Building for Scala 2.11.8
```
./dev/change-scala-version.sh 2.11
./build/mvn -Pyarn -Phadoop-2.6 -Dscala-2.11 -DskipTests clean package
```
* ####  Building With Hive and JDBC Support
```
 ~~Apache Hadoop 2.6.X with Hive 1.2.1 support ~~
./build/mvn -Pyarn -Phadoop-2.6 -Dhadoop.version=2.6.0 -Phive -Phive-thriftserver -DskipTests clean package
```
***
* ### spark启动
```
 spark-shell --master local[2]
      //2是两个线程
```
* #### spark实现wc
```
val file = sc.textFile("file:///home/sotowang/user/note/scala/scala相关")
val a = file.flatMap(line => line.split(" "))
val b = a.map(word => (word,1))
val c = b.reduceByKey(_+_)
```
>sc.textFile("file:///home/sotowang/user/note/scala/scala相关").flatMap(line => line.split(" ")).map(word => (word,1)).reduceByKey(_+_).collect

* ### WebUi
> http://sotowang-pc:4040/jobs/


#### 配置文件
/conf/spark-env.sh

```
export SPARK_DIST_CLASSPATH=$(hadoop classpath)
export SPARK_MASTER_IP=sotowang-pc
export SPARK_WORKER_CORES=1
export SPARK_WORKER_MEMORY=500m
export SPARK_WORKER_INSTANCES=1
```



#### 启动
>./start-master.sh 	

#### 基本RDD转换运算
##### 创建intRDD
*  创建intRDD

```
val intRDD= sc.parallelize(List(3,1,2,5,5))
```

* intRDD转换为Array

```
intRDD.collect()
```

* 创建stringRDD

```
val stringRDD = sc.parallelize(List("Apple","Orange","Banna","Grape","Apple"))
```

* 将stringRDD转换为Array

```
stringRDD.collect()
```

##### map运算
* 具名函数语句

```
def addOne(x:Int):Int={
return (x+1)
}
intRDD.map(addOne).collect()
```

> 1. 先定义addOne函数并传入参数x，此函数会将x加1再返回
> 2. 然后将函数名称addOne作为参数传入map全从，map命令会将每一个元素加1，从而产生另外一个RDD
> 3. 不过因map是一个转换，不会立即执行，为方便示范，将collect()这个动作运算使之立即执行

* 匿名函数

```
intRDD.map(x => x+1).collect()
```

* 匿名函数+匿名参数的语句

```
intRDD.map(_+1).collect()
```

##### map字符串运算
* 将stringRDD所有字符串元素前面加上fruit:,来产生一个RDD

```
stringRDD.map(x=>"fruit:"+x).collect
```

##### filter 数字运算
* 让intRdd 筛选数字小于3

```
intRDD.filter(x=>x<3).collect
```

* 使用匿名函数让筛选数字小于3，可使用下划线“_” 来取代 x=> x<3

```
intRDD.filter(_ < 3).collect
```

##### filter 字符串运算

```
stringRDD.filter(x=>x.contains("ra")).collect()
```

##### distince运算
>distinct运算会删除重复元素

* 删除intRDD重复元素

```
intRDD.distinct().collect()
```

* 删除stringRDD重复元素

```
stringRDD.distinct().collect()
```

##### randomSplit运算
* 以随机数数的方式按照4:6分割两个RDD

```
val sRDD = intRDD.randomSplit(Array(0.4,0.6))
```

* 读取分割后的两个RDD

```
sRDD(0).collect
sRDD(1).collect
```

##### groupBy运算
* 使用groupBy运算将整个集合分成奇数与偶数

```
val gRDD=intRDD.groupBy(
x=>{if (x%2 == 0) "even" else "odd"}
).collect
```

* 读取第一个是偶数的Array

```
gRDD(0)
```

* 读取第二个是奇数的Array

```
gRDD(1)
```

#### 多个RDD转换运算
##### 创建3个范例RDD
* 创建3个范例RDD

```
val intRDD1 = sc.parallelize(List(3,1,2,5,5))
val intRDD2 = sc.parallelize(List(5,6))
val intRDD3 = sc.parallelize(List(2,7))
```

##### union并集运算
* 使用union函数进行并集运算

```
intRDD1.union(intRDD2).union(intRDD3).collect()
```

* 使用++符号进行并集运算

```
(intRDD1 ++ intRDD2 ++ intRDD3).collect()
```

##### intersection交集运算
* 将intRDD1，intRDD2进行交集运算

```
intRDD1.intersection(intRDD2).collect()
```

##### subtract差集运算

```
intRDD1.subtract(intRDD2).collect
```

##### 笛卡尔乘积运算
* 进行cartesian 笛卡尔乘积运算

```
intRDD1.cartesian(intRDD2).collect()
```

#### 基本动作运算
##### 读取元素
* 读取第一条元素

```
intRDD.first
```

* 读取前几条元素

```
intRDD.take(2)
```

* 按照从小到大排序读取前N条数据，例如前3条

```
intRDD.takeOrdered(3)
```

* 按照从大到小排序读取前N条数据，例如前3条

```
intRDD.takeOrdered(3)(Ordering[Int].reverse)
```

##### 统计功能
* 统计

```
intRDD.stats
```

* 最小

```
intRDD.min
```

* 最大

```
intRDD.max
```

* 标准偏差

```
intRDD.stdev
```

* 计数

```
intRDD.count
```

* 求和

```
intRDD.sum
```

* 平均

```
intRDD.mean
```

#### RDD Key-Value 基本“转换”运算
##### 创建范例Key-Value RDD
* 创建范例Key-Value RDD

```
val kvRDD1 = sc.parallelize(List((3,4),(3,6),(5,6),(1,2)))
```

##### 列出keys的值
* 列出kvRDD1所有的Key值

```
kvRDD1.keys.collect()
```

##### 列出values的值
* 列出kvRDD1所有的values值

```
kvRDD1.values.collect()
```

##### 使用filter筛选key运算
* 使用filter筛选出key<5

```
kvRDD1.filter{
case (key,value)=>key<5
}.collect()
```

##### 使用filter筛选value运算
* 使用filter筛选出value<5

```
kvRDD1.filter{
case (key,value)=>value<5
}.collect()
```

##### mapValues运算
* 将每一组(Key,Value)的value值进行平方运算

```
kvRDD1.mapValues(x=>x*x).collect
```

##### sortByKey从小到大按照Key排序
* 从小到大按照Key排序

```
kvRDD1.sortByKey(true).collect()
kvRDD1.sortByKey().collect()  //默认参数为true
```

##### sortByKey从大到小按照Key排序
* 从大到小按照Key排序

```
kvRDD1.sortByKey(false).collect()
```

##### reduceByKey
* 使用匿名函数reduceByKey命令

```
kvRDD1.reduceByKey((x,y)=>x+y).collect
```

* 使用匿名函数+ 匿名参数reduceByKey命令

```
kvRDD1.reduceByKey(_+_).collect
```

#### 多个RDD Key-Value "转换" 运算
##### Key-Value RDD范例
* 创建范例Key-value RDD

```
val  kvRDD1 = sc.parallelize(List((3,4),(3,6),(5,6),(1,2)))
val  kvRDD2 = sc.parallelize(List((3,8)))
```

##### Key-Value RDD hoin运算
> join运算可以将两个RDD按照相同的key值join起来

```
kvRDD1.join(kvRDD2).foreach(println)
```

##### Key-Value leftOuterJoin运算
* kvRDD1 left join kvRDD2并且输出每一条数据

```
kvRDD1.leftOuterJoin(kvRDD2).foreach(println)
```

##### Key-Value rightOuterJoin运算
* kvRDD1 right join kvRDD2并且输出每一条数据

```
kvRDD1.rightOuterJoin(kvRDD2).foreach(println)
```

##### Key-Value subtractByKey运算
* kvRDD1 subtract kvRDD2

```
kvRDD1.subtractByKey(kvRDD2).collect
```

#### Key-Value "动作"运算
##### Key-Valuefirst 运算
* 读取第一条数据

```
kvRDD1.first()
```

* 读取前N条数据

```
kvRDD1.take(2)
```

##### 读取第一条数据的元素
* 读取第一条数据

```
val kvFirst = kvRDD1.first
```

* 读取第一条数据的第一个元素,也就是key值

```
kvFirst._1
```

* 读取第一条数据的第二个元素,也就是value值

```
kvFirst._2
```

##### Key-Value countByKey运算
* 计算每一个Key值的条数

```
kvRDD1.countByKey()
```

##### Key-Value collectAsMap运算
*  创建Key-Value对照表

```
var KV=kvRDD1.collectAsMap()
```

##### 使用对照表转换数据
* 使用对照表转换数据

```
KV(3)
KV(1)
```

##### Key-Value lookup运算
* 输入key值3,找到2条(3,4),(3,6)

```
kvRDD1.lookup(3)
kvRDD1.lookup(5)
```

#### Broadcast 广播变量
##### 不使用Broadcast 广播变量的范例
* 先创建kvFruit

```
val kvFruit=sc.parallelize(List((1,"apple"),(2,"orange"),(3,"banana"),(4,"grape")))
```

* 创建fruitMap对照表

```
val fruitMap = kvFruit.collectAsMap()
```

* 创建fruitIds

```
val fruitIds = sc.parallelize(List(2,4,1,3))
```

* 使用fruitMap对照表进行转换

```
val fruitNames = fruitIds.map(x=>fruitMap(x)).collect
```

##### 使用Broadcast广播变量的范例
>使用规则:	
>1. 可以使用SparkContext.broadcase([初始值])创建
>2. 使用.value方法来读取广播变量的值
>3. Broadcast广播变量被创建后,就不可以被修改

* 创建kvFruit

```
val kvFruit=sc.parallelize(List((1,"apple"),(2,"orange"),(3,"banana"),(4,"grape")))
```

* 创建fruitMap对照表

```
val fruitMap = kvFruit.collectAsMap()
```

* 创建 bcFruitMap广播变量

```
val bcFruitMap = sc.broadcast(fruitMap)
```

* 再创建fruitIds

```
val fruitIds = sc.parallelize(List(2,4,1,3))
```

* 使用bcFruitMap.value(x) 广播变量进行转换

```
val fruitNames = fruitIds.map(x=>bcFruitMap.value(x)).collect
```

#### accumulator累加器
##### accumulator 累加器范例
* 创建范例RDD

```
val intRDD = sc.parallelize(List(3,1,2,5,5))
```

*创建total累加器

```
val total = sc.accumulator(0.0)
```

*创建num累加器

```
val num = sc.accumulator(0)
```

* 使用foreach 传入参数i,针对每一条数据执行

```
intRDD.foreach(i=>{
total +=i
num +=1
})
```

* 显示求和,计数

```
println("total="+total.value+",num="+ num.value)
```

*计算平均

```
val avg = total.value/num.value
```

#### RDD Persistence持久化
##### RDD Persistence介绍
> 可以人用于将需要重复运算的RDD存储在内存中,以便大幅提升运算效率

##### RDD.persist() 范例
* 创建范例RDD

```
val intRddMemory = sc.parallelize(List(3,1,2,5,5))
```

* 将 intRddMemory 持久化

```
intRddMemory.persist()
```

* 将intRddMemory 取消持久化

```
intRddMemory.unpersist()
```

##### RDD.persist 设置存储等级范例
* 设置存储等级必须先导入相关链接库

```
import org.apache.spark.storage.StorageLevel
```

* 创建范例RDD

```
val intRddMemoryAndDisk = sc.parallelize(List(3,1,2,5,5))
```

* 设置选项为StorageLevel.MEMORY_AND_DISK

```
intRddMemoryAndDisk.persist(StorageLevel.MEMORY_AND_DISK)
```

* 将intRddMemoryAndDisk 取消持久化

```
intRddMemoryAndDisk.unpersist()
```

#### 使用Spark创建WordCount
#### 执行WordCount spark命令
* 读取文本文件

```
val textFile = sc.textFile("file:///home/sotowang/Desktop/test.txt")
```

* 使用flatMap空格分隔单词,并读取每个单词

```
val stringRDD = textFile.flatMap(line => line.split(" "))
```

*通过map reduce计算每一个单词出现的次数

```
val countsRDD = stringRDD.map(word => (word,1)).reduceByKey(_+_)
```

* 保存计算结果

```
countsRDD.saveAsTextFile("file:///home/sotowang/Desktop/output.txt")
```

### Spark Streaming
#### linux 定时执行Python脚本

```
crontab -e
> */1 * * * * /home/sotowang/user/note/other/log_generator.sh

```