### Flume配置
* flume的关键就是写配置文件	
#### [官网](http://flume.apache.org/FlumeUserGuide.html#configuring-individual-components)

```
A. 配置Source	
B. 配置Channel	
C. 配置Sink	
D. 把以上三个组件串起来	
```

```
a1: agent名称
r1: source名称
k1: sink名称
c1: channel名称
```

```
# example.conf: A single-node Flume configuration

# Name the components on this agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = netcat
a1.sources.r1.bind = localhost
a1.sources.r1.port = 44444

# Describe the sink
a1.sinks.k1.type = logger

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1

```

##### 启动agent

```
flume-ng agent \
--name a1 \
--conf $FLUME_HOME/conf  \
--conf-file $FLUME_HOME/conf/example.conf \
-Dflume.root.logger=INFO,console
```

##### 使用telnet进行测试
>telnet sotowang-pc 44444

#### Agent选型
> exec source + memory channel + logger sink

#### [教程](http://flume.apache.org/FlumeUserGuide.html#exec-source)
```
# example.conf: A single-node Flume configuration

# Name the components on this agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = exec
a1.sources.r1.command = tail -F /home/sotowang/user/note/other/data
a1.sources.tailsource-1.shell = /usr/bin/zsh -c

# Describe the sink
a1.sinks.k1.type = logger

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

##### 启动agent

```
flume-ng agent \
--name a1 \
--conf $FLUME_HOME/conf  \
--conf-file $FLUME_HOME/conf/exec-memory-logger.conf \
-Dflume.root.logger=INFO,console
```

#### 技术选型
> exec source + memory channel + avro sink	
> avro source + memory channel + logger

#### [教程](http://flume.apache.org/FlumeUserGuide.html#avro-source)

* exec-memory-avro.conf

```
# example.conf: A single-node Flume configuration

# Name the components on this agent
exec-memory-avro.sources = exec-source
exec-memory-avro.sinks = avro-sink
exec-memory-avro.channels = memory-channel

# Describe/configure the source
exec-memory-avro.sources.exec-source.type = exec
exec-memory-avro.sources.exec-source.command = tail -F /home/sotowang/user/note/other/data
exec-memory-avro.sources.exec-source.shell = /usr/bin/zsh -c

# Describe the sink
exec-memory-avro.sinks.avro-sink.type = avro
exec-memory-avro.sinks.avro-sink.hostname = sotowang-pc
exec-memory-avro.sinks.avro-sink.port = 44444

# Use a channel which buffers events in memory
exec-memory-avro.channels.memory-channel.type = memory


# Bind the source and sink to the channel
exec-memory-avro.sources.exec-source.channels = memory-channel
exec-memory-avro.sinks.avro-sink.channel = memory-channel
```

* avro-memory-logger.conf

```
# example.conf: A single-node Flume configuration

# Name the components on this agent
avro-memory-logger.sources = avro-source
avro-memory-logger.sinks = logger-sink
avro-memory-logger.channels = memory-channel

# Describe/configure the source
avro-memory-logger.sources.avro-source.type = avro
avro-memory-logger.sources.avro-source.bind = sotowang-pc
avro-memory-logger.sources.avro-source.port = 44444

# Describe the sink
avro-memory-logger.sinks.logger-sink.type = logger

# Use a channel which buffers events in memory
avro-memory-logger.channels.memory-channel.type = memory


# Bind the source and sink to the channel
avro-memory-logger.sources.avro-source.channels = memory-channel
avro-memory-logger.sinks.logger-sink.channel = memory-channel
```

##### 启动agent

* 先启动avro-memory-logger
```
flume-ng agent \
--name avro-memory-logger \
--conf $FLUME_HOME/conf  \
--conf-file $FLUME_HOME/conf/avro-memory-logger.conf \
-Dflume.root.logger=INFO,console
```

* 再启动exec-memory-avro.conf
```
flume-ng agent \
--name exec-memory-avro \
--conf $FLUME_HOME/conf  \
--conf-file $FLUME_HOME/conf/exec-memory-avro.conf \
-Dflume.root.logger=INFO,console
```

### 整合Flume和Kafka
* avro-memory-kafka.conf

```
avro-memory-kafka.sources = avro-source
avro-memory-kafka.sinks = kafka-sink
avro-memory-kafka.channels = memory-channel

avro-memory-kafka.sources.avro-source.type = avro
avro-memory-kafka.sources.avro-source.bind = sotowang-pc
avro-memory-kafka.sources.avro-source.port = 44444

avro-memory-kafka.sinks.kafka-sink.type = org.apache.flume.sink.kafka.KafkaSink
avro-memory-kafka.sinks.kafka-sink.brokerList = localhost:9092
avro-memory-kafka.sinks.kafka-sink.topic = hello_topic
a1.sinks.k1.batchSize = 5
a1.sinks.k1.requiredAcks = 1

avro-memory-kafka.channels.memory-channel.type = memory

avro-memory-kafka.sources.avro-source.channels = memory-channel
avro-memory-kafka.sinks.kafka-sink.channel = memory-channel
```

##### 启动agent

* 先启动avro-memory-kafka
```
flume-ng agent \
--name avro-memory-kafka \
--conf $FLUME_HOME/conf  \
--conf-file $FLUME_HOME/conf/avro-memory-kafka.conf \
-Dflume.root.logger=INFO,console
```

* 再启动exec-memory-avro.conf
```
flume-ng agent \
--name exec-memory-avro \
--conf $FLUME_HOME/conf  \
--conf-file $FLUME_HOME/conf/exec-memory-avro.conf \
-Dflume.root.logger=INFO,console
```

---
#### Flume Agent 的编写: flume_push_streaming.conf

```
    simple-agent.sources = netcat-source
    simple-agent.sinks = avro-sink
    simple-agent.channels = memory-channel

    simple-agent.sources.netcat-source.type = netcat
    simple-agent.sources.netcat-source.bind = sotowang-pc
    simple-agent.sources.netcat-source.port = 44444

    simple-agent.sinks.avro-sink.type = avro
    simple-agent.sinks.avro-sink.hostname = sotowang-pc
    simple-agent.sinks.avro-sink.port = 41414

    simple-agent.channels.memory-channel.type = memory

    simple-agent.sources.netcat-source.channels = memory-channel
    simple-agent.sinks.avro-sink.channel = memory-channel
```
#### flume启动

```
flume-ng agent \
--name simple-agent \
--conf $FLUME_HOME/conf \
--conf-file $FLUME_HOME/conf/flume_push_streaming.conf \
-Dflume.root.logger=INFO,console &
```
#### submit启动
```
spark-submit \
--class com.soto.spark.sparkstreaming.FlumePushWordCount \
--master local \
--packages org.apache.spark:spark-streaming-flume_2.11:2.2.0 \
/home/sotowang/user/aur/hadoop/source/lib/spark-1.0-SNAPSHOT.jar \
sotowang-pc 41414
```
---
#### Pull方式整合
*  flume_pull_streaming.conf

```
    simple-agent.sources = netcat-source
    simple-agent.sinks = spark-sink
    simple-agent.channels = memory-channel

    simple-agent.sources.netcat-source.type = netcat
    simple-agent.sources.netcat-source.bind = sotowang-pc
    simple-agent.sources.netcat-source.port = 44444

    simple-agent.sinks.spark-sink.type = org.apache.spark.streaming.flume.sink.SparkSink
    simple-agent.sinks.spark-sink.hostname = sotowang-pc
    simple-agent.sinks.spark-sink.port = 41414

    simple-agent.channels.memory-channel.type = memory

    simple-agent.sources.netcat-source.channels = memory-channel
    simple-agent.sinks.spark-sink.channel = memory-channel
```

#### flume启动

```
flume-ng agent \
--name simple-agent \
--conf $FLUME_HOME/conf \
--conf-file $FLUME_HOME/conf/flume_pull_streaming.conf \
-Dflume.root.logger=INFO,console 
```

---

#### Flume与logger4j连接

* streaming.conf

```
agent1.sources=avro-source
agent1.sinks=log-sink
agent1.channels=logger-channel

agent1.sources.avro-source.type=avro
agent1.sources.avro-source.bind=0.0.0.0
agent1.sources.avro-source.port=41414

agent1.channels.logger-channel.type=memory

agent1.sinks.log-sink.type=logger

agent1.sources.avro-source.channels=logger-channel
agent1.sinks.log-sink.channel=logger-channel
```

#### flume启动

```
flume-ng agent \
--name agent1 \
--conf $FLUME_HOME/conf \
--conf-file $FLUME_HOME/conf/streaming.conf \
-Dflume.root.logger=INFO,console 
```
---

### 整合Flume和Kafka(2)_logger-->flume-->kafka
* streaming2.conf

```
agent1.sources=avro-source
agent1.sinks=kafka-sink
agent1.channels=logger-channel

agent1.sources.avro-source.type=avro
agent1.sources.avro-source.bind=0.0.0.0
agent1.sources.avro-source.port=41414

agent1.channels.logger-channel.type=memory

agent1.sinks.kafka-sink.type=org.apache.flume.sink.kafka.KafkaSink
agent1.sinks.kafka-sink.topic = streamingtopic
agent1.sinks.kafka-sink.brokerList = sotowang-pc:9092
agent1.sinks.kafka-sink.batchSize = 20
agent1.sinks.kafka-sink.requiredAcks = 1


agent1.sources.avro-source.channels=logger-channel
agent1.sinks.kafka-sink.channel=logger-channel

```

#### flume启动

```
flume-ng agent \
--name agent1 \
--conf $FLUME_HOME/conf \
--conf-file $FLUME_HOME/conf/streaming2.conf \
-Dflume.root.logger=INFO,console 
```

---
#### 对接Python日志产生器输出的日志到Flume
* streaming_project.conf	
选型：access.log ===> 控制台输出
exec ==> memory ==> logger

```
exec-memory-logger.sources = exec-source
exec-memory-logger.sinks = logger-sink
exec-memory-logger.channels = memory-channel

exec-memory-logger.sources.exec-source.type = exec
exec-memory-logger.sources.exec-source.command = tail -F /home/sotowang/user/note/other/access.log
exec-memory-logger.sources.exec-source.shell = /bin/sh -c

exec-memory-logger.channels.memory-channel.type = memory

exec-memory-logger.sinks.logger-sink.type = logger

exec-memory-logger.sources.exec-source.channels = memory-channel
exec-memory-logger.sinks.logger-sink.channel = memory-channel
```

#### flume启动

```
flume-ng agent \
--name exec-memory-logger \
--conf $FLUME_HOME/conf \
--conf-file $FLUME_HOME/conf/streaming_project.conf	 \
-Dflume.root.logger=INFO,console 
```

### 日志==>Flume ==> Kafka
* 启动 zkServer.sh start
* 启动Kafka Server
	> kafka-server-start.sh -daemon $KAFKA_HOME/config/server.properties
* 修改Flume配置文件 使得flume sink数据到Kafka

* streaming_project2.conf	

```
exec-memory-kafka.sources = exec-source
exec-memory-kafka.sinks = kafka-sink
exec-memory-kafka.channels = memory-channel

exec-memory-kafka.sources.exec-source.type = exec
exec-memory-kafka.sources.exec-source.command = tail -F /home/sotowang/user/note/other/access.log
exec-memory-kafka.sources.exec-source.shell = /bin/sh -c

exec-memory-kafka.channels.memory-channel.type = memory

exec-memory-kafka.sinks.kafka-sink.type = org.apache.flume.sink.kafka.KafkaSink
exec-memory-kafka.sinks.kafka-sink.brokerList = sotowang-pc:9092
exec-memory-kafka.sinks.kafka-sink.topic = streamingtopic
exec-memory-kafka.sinks.kafka-sink.batchSize = 5
exec-memory-kafka.sinks.kafka-sink.requiredAcks = 1

exec-memory-kafka.sources.exec-source.channels = memory-channel
exec-memory-kafka.sinks.kafka-sink.channel = memory-channel
```

*  flume启动

```
flume-ng agent \
--name exec-memory-kafka \
--conf $FLUME_HOME/conf \
--conf-file $FLUME_HOME/conf/streaming_project2.conf	 \
-Dflume.root.logger=INFO,console 
```

* Kafka消费者
> kafka-console-consumer.sh --zookeeper sotowang-pc:2181 --topic streamingtopic


* 启动HDFS
* 启动zookeeper
* 启动Hbase
* Hbase表设计

* 需求1
>创建表

```
create 'course_clickcount','info'
```

> Rowkey设计

```
day_courseid
```

* 需求2
>创建表

```
create 'course_search_clickcount','info'
```

> Rowkey设计

```
day_courseid
```

* 提交

```
spark-submit --master "local[5]" \
--jars $(echo /home/sotowang/user/aur/hadoop/app/hbase-1.2.0-cdh5.7.0/lib/*.jar | tr ' ' ',') \
--class com.soto.spark.project.StatStreamingApp \
--packages org.apache.spark:spark-streaming-kafka-0-10_2.11:2.3.2 \
/home/sotowang/user/aur/ide/idea/idea-IU-182.3684.101/workspace/spark-train/target/spark-1.0-SNAPSHOT.jar \
sotowang-pc:9092 streamingtopic

```

### maven 打包

```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-assembly-plugin</artifactId>
    <version>3.0.0</version>
    <configuration>
        <archive>
            <manifest>
                <mainClass></mainClass>
            </manifest>
        </archive>
        <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
        </descriptorRefs>
    </configuration>
    <executions>
        <execution>
            <id>make-assembly</id> <!-- this is used for inheritance merges -->
            <phase>package</phase> <!-- 指定在打包节点执行jar包合并操作 -->
            <goals>
                <goal>single</goal>
            </goals>
        </execution>
    </executions>
</plugin>

```