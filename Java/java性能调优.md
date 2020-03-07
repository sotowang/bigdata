# 内存泄漏

* 程序分配了内存,但在程序结束时未被释放,导致那部分内存不可用

  ``` java
  Student s1 = new Student();
  Student s2 = new Student();
  //在ArrayList里放入s1和s2,再将s1和s2置为null,此时两对象所占内存不会被释放
  ```

## 如何避免内存泄漏

* 尽早释放无用对象的引用
* 使用临时变量时,让引用在退出活动域后自动设置为null,暗示垃圾收集器收集该对象
* 程序进行字符串处理时,避免使用String,而使用StringBuffer,因每一个Stringcfqj都会独立占用内存一块区域

## 检查内存泄漏工具

* MemoryAnalyzer
  * Java堆转储文件分析工具,可帮助发现内存漏洞和减少内存消耗
* EclipseMAT
  * 开源的Java内存分析软件,查找内存泄漏,能容易找到大块内存并验证谁在一直占用它
* JProbe
  * 分析Java的内存泄漏

# 基于JDK命令行工具的监控

* JVM参数类型

  * 标准参数

    ```bash
    -help
    -server 
    -client
    -version 
    -showversion
    -cp -classpath
    ```

  * X参数(非标准化参数)

    ```bash
    -Xint: 解释执行
    -Xcomp: 每一次使用就编译成本地代码
    -Xmixed: 混合模式,JVM自己来商定是否编译成本地代码
    ```

  * XX参数

    * Boolean类型

      * -X:[+-]<name>表示启用或者禁用name属性

        ```bash
        -X:+UseConcMarkSweepGC
        -X:+UseG1GC
        ```

    * 非Boolean类型

      * -XX:<name>=<value>表示name属性的值是value

        ```bash
        -XX:MaxGCPauseMillis=500
        -XX:GCTimeRatio=19
        ```

      * -Xms

        * 等价于-XX:InitialHeapSize

      * -Xmx

        * 等价于-XX:MaxHeapSize

    

* 查看运行时JVM参数

  * -XX:+PrintFlagsInitial

  * -XX:+PrintFlagsFinal

    ```bash
      bool IgnoreUnrecognizedVMOptions               = false                               {product}
        uintx IncreaseFirstTierCompileThresholdAt       = 50                                  {product}
         bool IncrementalInline                         = true                                {C2 product}
        uintx InitialBootClassLoaderMetaspaceSize       = 4194304                             {product}
        uintx InitialCodeCacheSize                      = 2555904                             {pd product}
        // := 表示修改后的值
        uintx InitialHeapSize                          := 125829120                           {product}
        uintx InitialRAMFraction                        = 64                                  {product}
    ```

  * -XX:+UnlockExperimentalVMOptions解锁实验参数

  * -XX:+UnlockDiagnosticVMOptions解锁诊断参数

  * -XX:+PrintCommandLineFlags打印命令行参数

* 查看JVM统计信息

  * jps

  * jinfo

    ```bash
    //查看最大堆内存
    jinfo -flag MaxHeapSize PID
    //查看垃圾回收器
    jinfo -flag UseG1GC PID
    ```

  * jstat

    ```bash
    //类装载信息,每隔1000ms 输出10次
    jstat -class PID 1000 10
    //关于GC信息
    jsta -gc PID 
    //JIT编译信息
    jstat -compiler PID
    ```

* jmap+MAT 内存溢出

  * 如何导出内存映像文件

    * 内存溢出自动导出

      ```bash
      -XX:+HeapDumpOnOutOfMemoryError
      -XX:HeapDumpPath=./
      ```

    * jmap导出

      ```bash
      jmap -dump:format=b,file=heap.hprof PID
      ```

  * MAT分析内存溢出原因

* jstack 死循环与死锁

# 基于JVisualVM的可视化监控

* 监控本地Java进程
* 监控远程Java进程
* 监控本地Tomcat
* 监控远程Tomcat

# 基于Btrace和监控调试

# Tomcat性能监控与调优

* Tomcat debug
* Tomcat-manager监控Tomcat
* psi-probe监控Tomcat
* Tomcat调优

# Ngnix性能监控调优

* ngx_http_stub_status监控连接信息
* ngxtop监控请求信息
* ngx-rrd图形化监控

# JVM GC调优

* JVM内存结构
* JVM垃圾回收
* GC日志格式与可视化日志分析工具
* Tomcat的GC调优

# Java代码层调优

* JVM字节码指令与javap
* i++与++i,字符串+拼接原理
* 常用代码优化方法