# [分析 Java heap dump工具之IBM HeapAnalyzer](https://www.cnblogs.com/winner-0715/p/8632755.html)

我们使用下面的代码来生成堆转储快照文件

![img](https://images2018.cnblogs.com/blog/824490/201803/824490-20180323223352968-847891786.png)

JVM参数为：

```
-verbose:gc -Xms10M -Xmx10M -XX:+HeapDumpOnOutOfMemoryError
```

生成文件名为java_pid5192.hprof的堆转储文件。

运行命令打开HeapAnalyzer

```
java –Xmx[heapsize] –jar ha456.jar 
```

打开堆转储文件java_pid5192.hprof

![img](https://images2018.cnblogs.com/blog/824490/201803/824490-20180323224404247-115762064.png)

可以看到，堆内存的96.64%的空间被Test$OOMObject占用。

占用内存的对象找到了，就到代码中分析就可以了。