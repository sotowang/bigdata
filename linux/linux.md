# 查找特定的文件

>  find path [options] params

```shell
find ~ -name "target3.java"  //精确查找
find ~ -name "target*"   // 模糊查找文件
find ~ -iname "targer*"  //忽略大小写

man find  //更多关于 find 的指令说明
```

# 检索文件内容

>grep [options] pattern file

```shell
grep "hadoop" README*
```

# 管道操作符

可将指令连接起来，前一个指令的输出作为后一个指令的输入

```shell
find ~ | grep "Hadoop"
```

*  管道只处理正确的输出，不执行错误的输出
* 右边命令必须能够接收检索文件输入流，否则传递过程中数据会被抛弃
* sed,awk,grep,cut,head,top,less,more,wc,join,sort,split

> grep -o 只输出符合 RE 的字符串

```shell
grep 'partial\[true\]' *.info,log | grep -o 'engine\[[0-9a-z]\]'
```

>grep -v   //过滤掉指令本身

```shell
ps -ef | grep ’tomcat‘ | grep -v ’tomcat‘
```



# 对文件内容做统计

> awk [options] 'cmd' file

* 一次读取一行文本，按输入分隔符进行切片，切成多个组成部分
* 将切片直接保存在内建的变量中，$1,$2...($0表示行的全部)
* 支持对单个切片的判断，支持循环判断，默认分隔符为空格

```shell
awk '{print $1,$4}' README.txt  //打印第一个和第4个切片的内容
```

```shell
awk '$1=="tcp" && $2 == 1 {print $0}' netstat.txt
```

```shell
awk '($1=="tcp" && $2 == 1) || NR==1 {print $0}' netstat.txt  //带有表头的数据
```

```shell
awk -F "," '{print $2}' test.txt   //-F 以逗号为分隔符
```

```shell
grep 'partial\[true\]' *.info,log | grep -o 'engine\[[0-9a-z]\]' |
awk '{engineer[$1]++}END{for(i in enginearr) print i "\t" enginearr[i]}'
```

# 批量替换文件内容

> sed [option] 'sed command' filename

* 全名 stream editor ，流编辑器
* 适合用于对文本的编辑

```shell
sed 's/^Str/String/' replace.java     //将Str打头的字符替换为String，不改变文档内容
```

```shell
sed -i 's/^Str/String/' replace.java     //将Str打头的字符替换为String，改变文档内容
```

```shell
sed -i 's/\.$/\;/' replace.java  //将 ’.‘ 替换为’；‘
```

```shell
sed -i 's/Jack/me/' replace.java   //将Jack替换为me  但只能替换一个Jack
```

```shell
sed -i 's/Jack/me/g' replace.java   //将所有Jack替换为me
```

```shell
sed -i '/^ *$/d' replace.java   //删除空行
```

```shell
sed -i 'Integer/d' replace.java //删除Integer所在行
```



# 何看当前Linux系统有几颗物理CPU和每颗CPU的核数？

答：

```
[root@centos6 ~ 10:55 #35]# cat /proc/cpuinfo|grep -c 'physical id'
4
[root@centos6 ~ 10:56 #36]# cat /proc/cpuinfo|grep -c 'processor'
4
```

#  查看系统负载有两个常用的命令，是哪两个？这三个数值表示什么含义呢？

答：

```
[root@centos6 ~ 10:56 #37]# w
10:57:38 up 14 min,  1 user,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM              LOGIN@   IDLE   JCPU   PCPU WHAT
root     pts/0    192.168.147.1    18:44    0.00s  0.10s  0.00s w
[root@centos6 ~ 10:57 #38]# uptime
10:57:47 up 14 min,  1 user,  load average: 0.00, 0.00, 0.00
```

其中load average即系统负载，三个数值分别表示一分钟、五分钟、十五分钟内系统的平均负载，即平均任务数。

#  vmstat r, b, si, so, bi, bo 这几列表示什么含义呢？

答：

```
[root@centos6 ~ 10:57 #39]# vmstat
procs -----------memory---------- ---swap-- -----io---- --system-- -----cpu-----
r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
0  0      0 1783964  13172 106056    0    0    29     7   15   11  0  0 99  0  0
```

- r即running，表示正在跑的任务数
- b即blocked，表示被阻塞的任务数
- si表示有多少数据从交换分区读入内存
- so表示有多少数据从内存写入交换分区
- bi表示有多少数据从磁盘读入内存
- bo表示有多少数据从内存写入磁盘

简记：

- i --input，进入内存
- o --output，从内存出去
- s --swap，交换分区
- b --block，块设备，磁盘

单位都是KB



# linux系统里，您知道buffer和cache如何区分吗？

答：

buffer和cache都是内存中的一块区域，当CPU需要写数据到磁盘时，由于磁盘速度比较慢，所以CPU先把数据存进buffer，然后CPU去执行其他任务，buffer中的数据会定期写入磁盘；当CPU需要从磁盘读入数据时，由于磁盘速度比较慢，可以把即将用到的数据提前存入cache，CPU直接从Cache中拿数据要快的多。

# 常用linux命令

* 整机
  * `top`
    * 查看系统资源占用情况
  * `uptime`
    * 系统运行时间
    * `load average`负载情况
* CPU
  * `vmstat -n 2 3`
    * 2:采样的时间间隔数（s）；3:采样的次数
    * `us+sy>80`说明cpu不足
  * `mpstat -P ALL 2`
    * 查看所有CPU核信息
* 内存
  * `free -m`查看总的内存
  * `pidstat -p PID -r 采样间隔秒数`
* 硬盘
  * `df -h`
    * h表示human
* 磁盘IO
  * `iostat -xdk 2 3`
* 网络IO
  * `ifstat`

# CPU占用过高分析

* `top`命令查出占用过高进程
* `ps -ef|grep java`定位到具体Java程序
* `ps -mp PID -o THREAD,tid,time`定位到具体线程
  * `-m`显示所有进程
  * `-p`pid进程使用cpu的时间
  * `-o`用户自定义格式
* `jstack 进程ID |grep tid(16进制线程ID小写英文)-A60`

















 