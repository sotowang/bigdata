# IO类型

* 同步IO
  * BIO阻塞IO
  * 非阻塞IO
  * IO多路复用
  * 信号驱动IO
* 异步IO

# BIO

- InputStream
- OutputStream
- Reader
- Writer

# NIO

* 与IO区别
  - 1)IO是面向流的，NIO是面向缓冲区的；
  - 2)IO流是阻塞的，NIO流是不阻塞的;
  - 3)NIO有选择器，而IO没有。
* 核心组件
  * Channels
    * Scatter: 从一个Channel读取的信息分散到N个缓冲区中(Buufer).
    * Gather: 将N个Buffer里面内容按照顺序发送到一个Channel.
  * Buffers
    * Java NIO Buffers用于和NIO Channel交互。 我们从Channel中读取数据到buffers里，从Buffer把数据写入到Channels；
    * Buffer本质上就是一块内存区；
    * 一个Buffer有三个属性是必须掌握的，分别是：capacity容量、position位置、limit限制。
    * **Buffer的常见方法**
      - Buffer clear()
      - Buffer flip()
      - Buffer rewind()
      - Buffer position(int newPosition)
  * Selectors
    * Selector 一般称 为选择器 ，当然你也可以翻译为 多路复用器 。它是Java NIO核心组件中的一个，用于检查一个或多个NIO Channel（通道）的状态是否处于可读、可写。如此可以实现单线程管理多个channels,也就是可以管理多个网络链接。
    * 使用Selector的好处在于： **使用更少的线程来就可以来处理通道**了， 相比使用多个线程，避免了线程上下文切换带来的开销。

# NIO是怎么工作的

# NIO在客户端的魔力

通过上面的分析，可以看出NIO在服务端对于解放线程，优化I/O和处理海量连接方面，确实有自己的用武之地。那么在客户端上，NIO又有什么使用场景呢?

常见的客户端BIO+连接池模型，可以建立n个连接，然后当某一个连接被I/O占用的时候，可以使用其他连接来提高性能。

但多线程的模型面临和服务端相同的问题：如果指望增加连接数来提高性能，则连接数又受制于线程数、线程很贵、无法建立很多线程，则性能遇到瓶颈。

## 每连接顺序请求的Redis

对于Redis来说，由于服务端是全局串行的，能够保证同一连接的所有请求与返回顺序一致。这样可以使用单线程＋队列，把请求数据缓冲。然后pipeline发送，返回future，然后channel可读时，直接在队列中把future取回来，done()就可以了。

伪代码如下：

```java
class RedisClient Implements ChannelHandler{
 private BlockingQueue CmdQueue;
 private EventLoop eventLoop;
 private Channel channel;
 class Cmd{
  String cmd;
  Future result;
 }
 public Future get(String key){
   Cmd cmd= new Cmd(key);
   queue.offer(cmd);
   eventLoop.submit(new Runnable(){
        List list = new ArrayList();
        queue.drainTo(list);
        if(channel.isWritable()){
         channel.writeAndFlush(list);
        }
   });
}
 public void ChannelReadFinish(Channel channel，Buffer Buffer){
    List result = handleBuffer();//处理数据
    //从cmdQueue取出future，并设值，future.done();
}
 public void ChannelWritable(Channel channel){
   channel.flush();
}
}
```

这样做，能够充分的利用pipeline来提高I/O能力，同时获取异步处理能力。

## 多连接短连接的HttpClient

类似于竞对抓取的项目，往往需要建立无数的HTTP短连接，然后抓取，然后销毁，当需要单机抓取上千网站线程数又受制的时候，怎么保证性能呢?

何不尝试NIO，单线程进行连接、写、读操作？如果连接、读、写操作系统没有能力处理，简单的注册一个事件，等待下次循环就好了。

如何存储不同的请求/响应呢？由于http是无状态没有版本的协议，又没有办法使用队列，好像办法不多。比较笨的办法是对于不同的socket，直接存储socket的引用作为map的key。

## 常见的RPC框架，如Thrift，Dubbo

这种框架内部一般维护了请求的协议和请求号，可以维护一个以请求号为key，结果的result为future的map，结合NIO+长连接，获取非常不错的性能。

# NIO高级主题

## Proactor与Reactor

一般情况下，I/O 复用机制需要事件分发器（event dispatcher）。 事件分发器的作用，即将那些读写事件源分发给各读写事件的处理者，就像送快递的在楼下喊: 谁谁谁的快递到了， 快来拿吧！开发人员在开始的时候需要在分发器那里注册感兴趣的事件，并提供相应的处理者（event handler)，或者是回调函数；事件分发器在适当的时候，会将请求的事件分发给这些handler或者回调函数。

涉及到事件分发器的两种模式称为：Reactor和Proactor。 Reactor模式是基于同步I/O的，而Proactor模式是和异步I/O相关的。在Reactor模式中，事件分发器等待某个事件或者可应用或个操作的状态发生（比如文件描述符可读写，或者是socket可读写），事件分发器就把这个事件传给事先注册的事件处理函数或者回调函数，由后者来做实际的读写操作。

而在Proactor模式中，事件处理者（或者代由事件分发器发起）直接发起一个异步读写操作（相当于请求），而实际的工作是由操作系统来完成的。发起时，需要提供的参数包括用于存放读到数据的缓存区、读的数据大小或用于存放外发数据的缓存区，以及这个请求完后的回调函数等信息。事件分发器得知了这个请求，它默默等待这个请求的完成，然后转发完成事件给相应的事件处理者或者回调。举例来说，在Windows上事件处理者投递了一个异步IO操作（称为overlapped技术），事件分发器等IO Complete事件完成。这种异步模式的典型实现是基于操作系统底层异步API的，所以我们可称之为“系统级别”的或者“真正意义上”的异步，因为具体的读写是由操作系统代劳的。

举个例子，将有助于理解Reactor与Proactor二者的差异，以读操作为例（写操作类似）。

#### 在Reactor中实现读

- 注册读就绪事件和相应的事件处理器。
- 事件分发器等待事件。
- 事件到来，激活分发器，分发器调用事件对应的处理器。
- 事件处理器完成实际的读操作，处理读到的数据，注册新的事件，然后返还控制权。

#### 在Proactor中实现读：

- 处理器发起异步读操作（注意：操作系统必须支持异步IO）。在这种情况下，处理器无视IO就绪事件，它关注的是完成事件。
- 事件分发器等待操作完成事件。
- 在分发器等待过程中，操作系统利用并行的内核线程执行实际的读操作，并将结果数据存入用户自定义缓冲区，最后通知事件分发器读操作完成。
- 事件分发器呼唤处理器。
- 事件处理器处理用户自定义缓冲区中的数据，然后启动一个新的异步操作，并将控制权返回事件分发器。

可以看出，两个模式的相同点，都是对某个I/O事件的事件通知（即告诉某个模块，这个I/O操作可以进行或已经完成)。在结构上，两者也有相同点：事件分发器负责提交IO操作（异步)、查询设备是否可操作（同步)，然后当条件满足时，就回调handler；不同点在于，异步情况下（Proactor)，当回调handler时，表示I/O操作已经完成；同步情况下（Reactor)，回调handler时，表示I/O设备可以进行某个操作（can read 或 can write)。

下面，我们将尝试应对为Proactor和Reactor模式建立可移植框架的挑战。在改进方案中，我们将Reactor原来位于事件处理器内的Read/Write操作移至分发器（不妨将这个思路称为“模拟异步”），以此寻求将Reactor多路同步I/O转化为模拟异步I/O。以读操作为例子，改进过程如下：

- 注册读就绪事件和相应的事件处理器。并为分发器提供数据缓冲区地址，需要读取数据量等信息。
- 分发器等待事件（如在select()上等待）。
- 事件到来，激活分发器。分发器执行一个非阻塞读操作（它有完成这个操作所需的全部信息），最后调用对应处理器。
- 事件处理器处理用户自定义缓冲区的数据，注册新的事件（当然同样要给出数据缓冲区地址，需要读取的数据量等信息），最后将控制权返还分发器。
  如我们所见，通过对多路I/O模式功能结构的改造，可将Reactor转化为Proactor模式。改造前后，模型实际完成的工作量没有增加，只不过参与者间对工作职责稍加调换。没有工作量的改变，自然不会造成性能的削弱。对如下各步骤的比较，可以证明工作量的恒定：

#### 标准/典型的Reactor：

- 步骤1：等待事件到来（Reactor负责）。
- 步骤2：将读就绪事件分发给用户定义的处理器（Reactor负责）。
- 步骤3：读数据（用户处理器负责）。
- 步骤4：处理数据（用户处理器负责）。

#### 改进实现的模拟Proactor：

- 步骤1：等待事件到来（Proactor负责）。

- 步骤2：得到读就绪事件，执行读数据（现在由Proactor负责）。

- 步骤3：将读完成事件分发给用户处理器（Proactor负责）。

- 步骤4：处理数据（用户处理器负责）。

  对于不提供异步I/O API的操作系统来说，这种办法可以隐藏Socket API的交互细节，从而对外暴露一个完整的异步接口。借此，我们就可以进一步构建完全可移植的，平台无关的，有通用对外接口的解决方案。

代码示例如下：

```
interface ChannelHandler{
      void channelReadComplate(Channel channel，byte[] data);
      void channelWritable(Channel channel);
   }
   class Channel{
     Socket socket;
     Event event;//读，写或者连接
   }

   //IO线程主循环：
   class IoThread extends Thread{
   public void run(){
   Channel channel;
   while(channel=Selector.select()){//选择就绪的事件和对应的连接
      if(channel.event==accept){
         registerNewChannelHandler(channel);//如果是新连接，则注册一个新的读写处理器
         Selector.interested(read);
      }
      if(channel.event==write){
         getChannelHandler(channel).channelWritable(channel);//如果可以写，则执行写事件
      }
      if(channel.event==read){
          byte[] data = channel.read();
          if(channel.read()==0)//没有读到数据，表示本次数据读完了
          {
          getChannelHandler(channel).channelReadComplate(channel，data;//处理读完成事件
          }
          if(过载保护){
          Selector.interested(read);
          }

      }
     }
    }
   Map<Channel，ChannelHandler> handlerMap;//所有channel的对应事件处理器
   }
```

## Selector.wakeup()

### 主要作用

解除阻塞在Selector.select()/select(long)上的线程，立即返回。

两次成功的select之间多次调用wakeup等价于一次调用。

如果当前没有阻塞在select上，则本次wakeup调用将作用于下一次select——“记忆”作用。

为什么要唤醒？

注册了新的channel或者事件。

channel关闭，取消注册。

优先级更高的事件触发（如定时器事件），希望及时处理。

### 原理

Linux上利用pipe调用创建一个管道，Windows上则是一个loopback的tcp连接。这是因为win32的管道无法加入select的fd set，将管道或者TCP连接加入select fd set。

wakeup往管道或者连接写入一个字节，阻塞的select因为有I/O事件就绪，立即返回。可见，wakeup的调用开销不可忽视。

## Buffer的选择

通常情况下，操作系统的一次写操作分为两步：

1. 将数据从用户空间拷贝到系统空间。
2. 从系统空间往网卡写。同理，读操作也分为两步：
   ① 将数据从网卡拷贝到系统空间；
   ② 将数据从系统空间拷贝到用户空间。

对于NIO来说，缓存的使用可以使用DirectByteBuffer和HeapByteBuffer。如果使用了DirectByteBuffer，一般来说可以减少一次系统空间到用户空间的拷贝。但Buffer创建和销毁的成本更高，更不宜维护，通常会用内存池来提高性能。

如果数据量比较小的中小应用情况下，可以考虑使用heapBuffer；反之可以用directBuffer。

# NIO存在的问题

使用NIO != 高性能，当连接数<1000，并发程度不高或者局域网环境下NIO并没有显著的性能优势。

NIO并没有完全屏蔽平台差异，它仍然是基于各个操作系统的I/O系统实现的，差异仍然存在。使用NIO做网络编程构建事件驱动模型并不容易，陷阱重重。

推荐大家使用成熟的NIO框架，如Netty，MINA等。解决了很多NIO的陷阱，并屏蔽了操作系统的差异，有较好的性能和编程模型。

# 总结

最后总结一下到底NIO给我们带来了些什么：

> - 事件驱动模型
> - 避免多线程
> - 单线程处理多任务
> - 非阻塞I/O，I/O读写不再阻塞，而是返回0
> - 基于block的传输，通常比基于流的传输更高效
> - 更高级的IO函数，zero-copy
> - IO多路复用大大提高了Java网络应用的可伸缩性和实用性

本文抛砖引玉，诠释了一些NIO的思想和设计理念以及应用场景，这只是从冰山一角。关于NIO可以谈的技术点其实还有很多，期待未来有机会和大家继续探讨。



# AIO

# BIO、NIO、AIO对比



| 属性/模型             | 阻塞BIO    | 非阻塞NIO    | 异步AIO      |
| --------------------- | ---------- | ------------ | ------------ |
| blocking              | 阻塞并同步 | 非阻塞但同步 | 非阻塞并异步 |
| 线程数(server:client) | 1:1        | 1:N          | 0:N          |
| 复杂度                | 简单       | 较复杂       | 复杂         |
| 吞吐量                | 低         | 高           | 高           |







