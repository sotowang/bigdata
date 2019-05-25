# **何看当前Linux系统有几颗物理CPU和每颗CPU的核数？**

答：

```
[root@centos6 ~ 10:55 #35]# cat /proc/cpuinfo|grep -c 'physical id'
4
[root@centos6 ~ 10:56 #36]# cat /proc/cpuinfo|grep -c 'processor'
4
```

#  **查看系统负载有两个常用的命令，是哪两个？这三个数值表示什么含义呢？**

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

#  **vmstat r, b, si, so, bi, bo 这几列表示什么含义呢？**

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



# **linux系统里，您知道buffer和cache如何区分吗？**

答：

buffer和cache都是内存中的一块区域，当CPU需要写数据到磁盘时，由于磁盘速度比较慢，所以CPU先把数据存进buffer，然后CPU去执行其他任务，buffer中的数据会定期写入磁盘；当CPU需要从磁盘读入数据时，由于磁盘速度比较慢，可以把即将用到的数据提前存入cache，CPU直接从Cache中拿数据要快的多。

# **使用top查看系统资源占用情况时，哪一列表示内存占用呢？**

答：

```
PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND
301 root      20   0     0    0    0 S  0.3  0.0   0:00.08 jbd2/sda3-8
1 root      20   0  2900 1428 1216 S  0.0  0.1   0:01.28 init
2 root      20   0     0    0    0 S  0.0  0.0   0:00.00 kthreadd
3 root      RT   0     0    0    0 S  0.0  0.0   0:00.00 migration/0
```

- VIRT虚拟内存用量
- RES物理内存用量
- SHR共享内存用量
- %MEM内存用量

 























 