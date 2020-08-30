# [常用linux命令](https://mp.weixin.qq.com/s/HtLwChoLzqhbM4pKldLDng)

```bash
ps aux|grep java 查看java进程
ps aux 查看所有进程
ps –ef|grep tomcat 查看所有有关tomcat的进程
ps -ef|grep --color java 高亮要查询的关键字
kill -9 19979 终止线程号位19979的进程
```

- 整机
  - `top`
    - 查看系统资源占用情况
  - `uptime`
    - 系统运行时间
    - `load average`负载情况
- CPU
  - `vmstat -n 2 3`
    - 2:采样的时间间隔数（s）；3:采样的次数
    - `us+sy>80`说明cpu不足
  - `mpstat -P ALL 2`
    - 查看所有CPU核信息
- 内存
  - `free -m`查看总的内存
  - `pidstat -p PID -r 采样间隔秒数`
- 硬盘
  - `df -h`
    - h表示human
- 磁盘IO
  - `iostat -xdk 2 3`
- 网络IO
  - `ifstat`

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



# 查看物理CPU和每颗CPU的核数

```shell
cat /proc/cpuinfo|grep -c 'physical id'
4
cat /proc/cpuinfo|grep -c 'processor'
4
```

#  查看系统负载有两个常用的命令

```shell
 w
10:57:38 up 14 min,  1 user,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM              LOGIN@   IDLE   JCPU   PCPU WHAT
root     pts/0    192.168.147.1    18:44    0.00s  0.10s  0.00s w
uptime
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

# buffer和cache如何区分

* CPU写数据到磁盘时，先将数据存入buffer
* CPU读数据时，先将数据存入cache

# [CPU占用过高分析](https://blog.csdn.net/u011277123/article/details/103495338)

* **定位进程**
  * 登录服务器，执行top命令，查看CPU占用情况
  * 通过以上命令，我们可以看到，进程ID为1893的Java进程的CPU占用率达到了181%，基本可以定位到是我们的Java应用导致整个服务器的CPU占用率飙升。
* **定位线程**
  * 通过top -Hp 1893命令，我们可以发现，当前1893这个进程中，ID为4519的线程占用CPU最高。
* **定位代码**
  * 通过jstack命令，查看栈信息
  * 通过以上代码，我们可以清楚的看到，BeanValidator.java的第30行是有可能存在问题的。



# 统计文件中出现次数最多的前10个词

```shell
cat words.txt |sort |uniq -c |sort -k1,1nr |head -10
```

* `sort` 对一行一个单词的列表进行排序
* `uniq -c`对排序好的单词列表统计每个单词出现的次数
* 再用`sort`按照单词出现的次数从大到小排序，若出现频率相同，再按字母顺序排序
* `sort -n `使用纯数字进行排序
* `sort -r`倒序
* `sork -k` 指定那个字段
* `sort -t`指定分隔符

# VIM

* 打开与退出

  * vi file： 打开文件 file
  * :q ： 退出 vi 编辑器
  * :wq： 保存缓冲区的修改并退出编辑器
  * :q!： 不保存直接退出
  * :w 保存缓冲区内容至默认的文件
  * :w file 保存缓冲区内容至 file 文件

* 插入文本

  * a : 在当前光标的右边插入文本
  * A : 在当前光标行的末尾插入文本
  * i : 在当前光标的左边插入文本
  * I : 在当前光标所在行的开始处插入文本
  * o: 在当前行在下面新建一行
  * O:在当前行的上面新建一行
  * R:替换当前光标位置以及以后的若干文本
  * J:连接光标所在行和下一行

* 删除文本

  * x: 删除一个字符
  * dd: 删除一行
  * ndd: 删除 n 行
  * u: 撤销上一次操作
  * U: 撤销对当前行的所有操作

* 搜索

  * /word 从前向后搜索第一个出现的 word

  * ？ word 从后向前搜索第一个出现的 word

* 设置行号

  * :set nu 在屏幕上显示行号
  * :set nonu 取消行号

# [Linux操作系统网络服务器模型](https://yq.aliyun.com/articles/131978)

* 并发服务器
  
  * 并发服务器就是在同一个时刻可以处理来自多个客户端的请求;
* 循环服务器
  * 循环服务器是指服务器在同一时刻指可以响应一个客户端的请求

  

# [SIGINT SIGTERM SIGKILL区别](https://www.jianshu.com/p/f6dfbf51c541)

* SIGINT SIGTERM SIGKILL都是结束/终止进程

* **SIGINT信号为ctrl+c发送出来的信号，该信号只能结束前台进程**，信号被当前进程树接收到，也就是说，不仅当前进程会收到信号，它的子进程也会收到；

* SIGTERM可以被阻塞，**kill不加任何参数的时候发送的就是这个信号，只有当前进程收到信号，子进程不会收到**。如果当前进程被kill了，那么它的子进程的父进程将会是init，也就是pid为1的进程；

* **SIGKILL是不能被捕获的，程序收到这个信号后，一定会退出。这就是kill -9一定能保证将程序杀死的原因。**

