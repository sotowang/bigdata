# [Java 动态代理作用是什么？](https://www.zhihu.com/question/20794107)

* 实现无侵入式的代码扩展,也就是方法增强,在不修改源码情况下,增强一些方法

# Java动态代理实现的两种方法

## [JDK动态代理](https://blog.csdn.net/xlgen157387/article/details/82497594?utm_medium=distribute.pc_relevant.none-task-blog-searchFromBaidu-5.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-searchFromBaidu-5.channel_param)

* JDK动态代理通过反射来接收被代理的类，并且要求被代理的类必须实现一个接口。JDK动态代理的核心是`InvocationHandler`接口和Proxy类。
  * 通过Proxy来生成代理类实例，而这个代理实例通过调用处理器`InvocationHandler`接收不同的参数灵活调用真实对象的方法。
  * `InvocationHandler`是一个接口，可以通过实现该接口定义横切逻辑，并通过反射机制调用目标类的代码，动态地将横切逻辑和业务逻辑贬值在一起。
* 目标类方法都要在接口中声明
* 如果目标类没有实现接口，那么Spring AOP会选择使用**CGLIB**来动态代理目标类。

## CGLIB动态代理

* CGLIB（Code Generation Library），是一个代码生成的类库，可以在运行时动态的生成某个类的子类，注意，CGLIB是通过继承的方式做的动态代理，因此**如果某个类被标记为final，那么它是无法使用CGLIB做动态代理**的。

* 借助asm来实现，在生成类之后的相关执行过程中比较高效（可通过将asm生成的类进行缓存解决asm生成类过程低效问题）

* CGLib可以为一个类创建一个子类，可以实现运行期动态扩展java类，Spring在运行期间通过 CGlib继承要被动态代理的类，重写父类的方法，实现AOP面向切面编程


## [CGLIB与JDK区别](https://blog.csdn.net/xlgen157387/article/details/82497594?utm_medium=distribute.pc_relevant.none-task-blog-searchFromBaidu-5.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-searchFromBaidu-5.channel_param)

* Spring通过Java的反射机制生产被代理接口的新的匿名实现类，重写了其中AOP的增强方法。

* JDK **动态代理类和委托类需要都实现同一个接口**。也就是说只有实现了某个接口的类可以使用Java动态代理机制.
* 但是，事实上使用中并不是遇到的所有类都会给你实现一个接口。因此，**对于没有实现接口的类，就不能使用该机制。而CGLIB则可以实现对类的动态代理**。

## [性能对比](https://blog.csdn.net/xlgen157387/article/details/82497594?utm_medium=distribute.pc_relevant.none-task-blog-searchFromBaidu-5.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-searchFromBaidu-5.channel_param)

1、CGLib所创建的动态代理对象在实际运行时候的性能要比JDK动态代理高不少，有研究表明，大概要高10倍；

2、但是CGLib在创建对象的时候所花费的时间却比JDK动态代理要多很多，有研究表明，大概有8倍的差距；

3、因此，对于singleton的代理对象或者具有实例池的代理，因为无需频繁的创建代理对象，所以比较适合采用CGLib动态代理，反正，则比较适用JDK动态代理。

在JDK1.8的时候，**JDK动态代理的速度已经比CGLib动态代理的速度快很多了**

# [JDK动态代理为什么必须用接口](https://juejin.im/post/5d8a0799f265da5b7a752e7c)

* 因为Java中不支持多继承，而JDK的动态代理在创建代理对象时，默认让代理对象继承了Proxy类，所以JDK只能通过接口去实现动态代理。