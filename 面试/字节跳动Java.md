**作者：索罗娃·多卡纳 链接：<https://www.nowcoder.com/discuss/203984> 来源：牛客网**

# 一面  

## cookie和session的区别  

* cookie 在客户端
* session在服务端
* 通过cookie实现session

## session的生命周期  

* session调用 inValidate
* session到期,maxAge=-1

## 有n个服务器，怎样去协调他们使用redis

分布式锁

set key value px ... nx 

## redis持久化方式  

* RDB
* AOF
* RDB-AOF(官方推荐)

## Mysql索引结构  

## 聊一聊Mysql查询优化（没回答上来。。。）  

例子  有ABCD，建立了index(A,C,D)  查A = xx and D = xx可以吗  查C = xx and D = xx可以吗  查C = xx and A = xx可以吗（我懵了）  

来搞一道算法题吧  

给定数组nums=[1,2,3,2,3,5,4],和一个常数K，找到有没有可能将nums分成k个子集，使得每个子集的和相等  (5),(1,4),(2,3),(2,3) True  写了快半小时，面试官说注意时间。。。。。然后我一顿瞎写，用的纯暴力算法。。。  面试官说还有很多可优化的。。。





 

https://www.nowcoder.com/discuss/210068

 

 

 

头条二面（2019-07-14）  

 	HTTPS和HTTP的区别  

 	java GC分为哪几种  

 	TCP/UDP区别  

 	TCP连接建立的过程  

 	要是服务器发送的最后一个报文时，客户端挂了会怎样  

 	A B C D 建立了A，C，D三个索引  

 	A = x and C = x 用到了几个索引  

 	算法题：求最长不重复子序列  

 	面试官评价，知识的广度可以，深度不够，很多东西都只知道表面，没有深入研究过  

 	头条三面（2019-07-18）  

 	约的6点的面试，5点38分打来电话说能上线吗，我说能  

 	上线后，面试官说实现一个堆排序可以吗，我说可以。那给你15分钟，然后他就走了。。。。  

 	跌跌撞撞到20分钟，他终于回来了，写的怎么样，给我讲讲，我balabala。。。  

 	mysql用过吗，Inoodb与MyISM的区别。我讲了一半，直接打断  

 	聊聊网络吧，TCP的TIMED_WAITING  

 	java多线程了解过吗，怎么写一个多线程（线程池可以吗）  

 	线程池有哪几个组件（我不知道。。。说了下参数）  

 	在哪个公司实习？讲讲业务逻辑  

 	核心回测模块有参与过吗（没有。。。瞎说）  

 	轮到你提问了，我？？？？（此时6点12）  	