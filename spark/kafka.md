### Kafka
#### [教程](http://kafka.apache.org/quickstart)

#### 启动Kafka
* 先启动Zookeeper

```
zookeeper-server-start.sh $KAFKA_HOME/config/zookeeper.properties
```

```
kafka-server-start.sh  -daemon $KAFKA_HOME/config/server.properties
```

#### 创建topic

```
kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic hello_topic
```

* We can now see that topic if we run the list topic command:

```
kafka-topics.sh --list --zookeeper localhost:2181
```

#### 发送消息

```
kafka-console-producer.sh --broker-list localhost:9092 --topic hello_topic
```

#### 消费消息

```
kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic hello_topic --from-beginning   
//--from-beginning 从头开始消费
```

* Okay but now that we have a cluster how can we know which broker is doing what? To see that run the "describe topics" command:
```
kafka-topics.sh --describe --zookeeper localhost:2181 
```

### 单节点多broker

```
kafka-server-start.sh -daemon $KAFKA_HOME/config/server-1.properties &

kafka-server-start.sh -daemon $KAFKA_HOME/config/server-2.properties &

kafka-server-start.sh -daemon $KAFKA_HOME/config/server-3.properties &

```

#### 创建一分区多副本

```
kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 1 --topic my-replicated-topic
```

#### 发送消息

```
kafka-console-producer.sh --broker-list localhost:9092,localhost:9093,localhost:9094 --topic my-replicated-topic
```

#### 消费消息

```
kafka-console-consumer.sh --zookeeper localhost:2181 --topic my-replicated-topic
```

