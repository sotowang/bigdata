# 无类别域间路由**（CIDR**）

**无类别域间路由**（Classless Inter-Domain Routing、**CIDR**）是一个用于给用户分配[IP地址](https://zh.wikipedia.org/wiki/IP%E5%9C%B0%E5%9D%80)以及在[互联网](https://zh.wikipedia.org/wiki/%E4%BA%92%E8%81%94%E7%BD%91)上有效地路由IP[数据包](https://zh.wikipedia.org/wiki/%E6%95%B0%E6%8D%AE%E5%8C%85)的对IP地址进行归类的方法。

在[域名系统](https://zh.wikipedia.org/wiki/%E5%9F%9F%E5%90%8D%E7%B3%BB%E7%BB%9F)出现之后的第一个十年里，基于[分类网络](https://zh.wikipedia.org/wiki/%E5%88%86%E7%B1%BB%E7%BD%91%E7%BB%9C)进行地址分配和路由IP数据包的设计就已明显显得[可扩充性](https://zh.wikipedia.org/wiki/%E5%8F%AF%E6%89%A9%E6%94%BE%E6%80%A7)不足 （参见RFC 1517）。为了解决这个问题，[互联网工程工作小组](https://zh.wikipedia.org/wiki/%E4%BA%92%E8%81%94%E7%BD%91%E5%B7%A5%E7%A8%8B%E5%B7%A5%E4%BD%9C%E5%B0%8F%E7%BB%84)在1993年发布了一新系列的标准——RFC 1518和RFC 1519——以定义新的分配IP地址块和路由[IPv4](https://zh.wikipedia.org/wiki/IPv4)数据包的方法。

一个IP地址包含两部分：标识网络的**前缀**和紧接着的在这个网络内的**主机地址**。在之前的[分类网络](https://zh.wikipedia.org/wiki/%E5%88%86%E7%B1%BB%E7%BD%91%E7%BB%9C)中，IP地址的分配把IP地址的32位按每8位为一段分开。这使得前缀必须为8，16或者24位。因此，可分配的最小的地址块有256（24位前缀，8位主机地址，28=256）个地址，而这对大多数企业来说太少了。大一点的地址块包含65536（16位前缀，16位主机，216=65536）个地址，而这对大公司来说都太多了。这导致不能充分使用IP地址和在路由上的不便，因为大量的需要单独路由的小型网络（C类网络）因在地域上分得很开而很难进行[聚合路由](https://zh.wikipedia.org/w/index.php?title=%E8%81%9A%E5%90%88%E8%B7%AF%E7%94%B1&action=edit&redlink=1)，于是给路由设备增加了很多负担。

无类别域间路由是基于**可变长子网掩码（VLSM）**来进行任意长度的前缀的分配的。在RFC 950（1985）中有关于可变长子网掩码的说明。CIDR包括：

- 指定任意长度的前缀的可变长子网掩码技术。遵从CIDR规则的地址有一个后缀说明前缀的位数，例如：192.168.0.0/16。这使得对日益缺乏的IPv4地址的使用更加有效。
- 将多个连续的前缀聚合成**超网**，以及，在互联网中，只要有可能，就显示为一个聚合的网络，因此在总体上可以减少路由表的表项数目。聚合使得互联网的路由表不用分为多级，并通过VLSM逆转“划分子网”的过程。
- 根据机构的实际需要和短期预期需要而不是分类网络中所限定的过大或过小的地址块来管理IP地址的分配的过程。

因为在[IPv6](https://zh.wikipedia.org/wiki/IPv6)中也使用了IPv4的用后缀指示前缀长度的CIDR，所以IPv4中的*分类*在IPv6中已不再使用。

# 任播（**anycast**）

**任播**（英语：**anycast**）是一种网络定址和[路由](https://zh.wikipedia.org/wiki/%E8%B7%AF%E7%94%B1)的策略，使得数据可以根据[路由拓扑](https://zh.wikipedia.org/wiki/%E7%BD%91%E7%BB%9C%E6%8B%93%E6%89%91)来决定送到“最近”或“最好”的目的地。

任播是与[单播](https://zh.wikipedia.org/wiki/%E5%96%AE%E6%92%AD)（unicast）、[广播](https://zh.wikipedia.org/wiki/%E5%BB%A3%E6%92%AD_(%E7%B6%B2%E8%B7%AF))（broadcast）和[多播](https://zh.wikipedia.org/wiki/%E5%A4%9A%E6%92%AD)（multicast）不同的方式。

- 在单播中，在网络地址和网络节点之间存在一一对应的关系。
- 在广播和多播中，在网络地址和网络节点之间存在一对多的关系：每一个发送地址对应一群接收可以复制信息的节点。
- 在任播中，在网络地址和网络节点之间存在一对多的关系：每一个地址对应一群接收节点，但在任何给定时间，只有其中之一可以接收到发送端来的信息。

在[互联网](https://zh.wikipedia.org/wiki/%E7%B6%B2%E9%9A%9B%E7%B6%B2%E8%B7%AF)中，通常使用[边界网关协议](https://zh.wikipedia.org/wiki/%E9%82%8A%E7%95%8C%E7%B6%B2%E9%97%9C%E5%8D%94%E8%AD%B0)（BGP）来实现任播。

在过去，任播适合无连线协议通常创建在[用户数据报协议](https://zh.wikipedia.org/wiki/%E7%94%A8%E6%88%B7%E6%95%B0%E6%8D%AE%E6%8A%A5%E5%8D%8F%E8%AE%AE)（UDP）多于连线导向协议（如会记录状态的[传输控制协议](https://zh.wikipedia.org/wiki/%E5%82%B3%E8%BC%B8%E6%8E%A7%E5%88%B6%E5%8D%94%E8%AD%B0)（TCP））。然而，也有很多情况是[传输控制协议](https://zh.wikipedia.org/wiki/%E5%82%B3%E8%BC%B8%E6%8E%A7%E5%88%B6%E5%8D%94%E8%AD%B0)（TCP）使用任播的，包含运载网络如[Prolexic](https://zh.wikipedia.org/w/index.php?title=Prolexic&action=edit&redlink=1)使用传输控制协议任播。

因此，任播通常用于提供高可靠性和负载平衡。

## IPv6过渡期中任播地址的使用

在[IPv4](https://zh.wikipedia.org/wiki/IPv4)到[IPv6](https://zh.wikipedia.org/wiki/IPv6)的过渡进程中，任播定址可能可以被用来发展提供IPv4兼容到IPv6主机。[6to4](https://zh.wikipedia.org/wiki/6to4)使用一个IP地址为192.88.99.1的默认网关（参见RFC 3068）。这允许多个实现6to4网关的提供者而不用主机知道每一个单独提供者的网关地址。


# 资源预留协议（RSVP）

资源预留协议（Resource ReSerVation Protocol；RSVP）是一种用于互联网上质量整合服务的协议。RSVP 允许[主机](https://baike.baidu.com/item/%E4%B8%BB%E6%9C%BA/455151)在网络上请求特殊服务质量用于特殊应用程序[数据流](https://baike.baidu.com/item/%E6%95%B0%E6%8D%AE%E6%B5%81/3002243)的传输。[路由器](https://baike.baidu.com/item/%E8%B7%AF%E7%94%B1%E5%99%A8/108294)也使用 RSVP 发送服务质量（QOS）请求给所有结点（沿着流[路径](https://baike.baidu.com/item/%E8%B7%AF%E5%BE%84/1081474)）并建立和维持这种状态以提供请求服务。

通常 RSVP 请求将会引起每个节点数据路径上的资源预留。

传输协议

路由选择协议

路由选择

 ![img](https://gss1.bdstatic.com/-vo3dSag_xI4khGkpoWK1HF6hhy/baike/c0%3Dbaike80%2C5%2C5%2C80%2C26/sign=0ebd32b82edda3cc1fe9b07260805264/962bd40735fae6cd3e0200460fb30f2442a70f66.jpg)

RFC2205对RSVP的特征做出以下的描述：

（1）支持[单播](https://baike.baidu.com/item/%E5%8D%95%E6%92%AD)与[组播](https://baike.baidu.com/item/%E7%BB%84%E6%92%AD)；

（2）单向预留；

（3）接收者发起预留；

（4）维护Internet中的软状态。

# 加权公平排队（WFQ）

WFQ是加权公平排队(Weighted Fair Queuing)缩写。它是一种[拥塞管理](https://baike.baidu.com/item/%E6%8B%A5%E5%A1%9E%E7%AE%A1%E7%90%86)算法，该算法识别对话（以[数据流](https://baike.baidu.com/item/%E6%95%B0%E6%8D%AE%E6%B5%81/3002243)的形式）、**分开属于各个对话的分组，并确保传输容量被这些独立的对话公平地分享**。WFQ是在发生拥塞时稳定网络运行的一种自动的方法，它能提高处理性能并减少分组的重发。









