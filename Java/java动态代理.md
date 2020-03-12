# [Java 动态代理作用是什么？](https://www.zhihu.com/question/20794107)

* 实现无侵入式的代码扩展,也就是方法增强,在不修改源码情况下,增强一些方法

# Java动态代理实现的两种方法

## JDK动态代理

* JDK动态代理通过反射来接收被代理的类，并且要求被代理的类必须实现一个接口。JDK动态代理的核心是`InvocationHandler`接口和Proxy类。
  * 通过Proxy来生成代理类实例，而这个代理实例通过调用处理器InvocationHandler接收不同的参数灵活调用真实对象的方法。
* 目标类方法都要在接口中声明
* 如果目标类没有实现接口，那么Spring AOP会选择使用**CGLIB**来动态代理目标类。

## CGLIB动态代理

* CGLIB（Code Generation Library），是一个代码生成的类库，可以在运行时动态的生成某个类的子类，注意，CGLIB是通过继承的方式做的动态代理，因此**如果某个类被标记为final，那么它是无法使用CGLIB做动态代理**的。

* 借助asm来实现，在生成类之后的相关执行过程中比较高效（可通过将asm生成的类进行缓存解决asm生成类过程低效问题）

* 必须实现MethodInterceptor(方法拦截器)接口

  * ```java
    //这个接口只有一个intercept()方法，这个方法有4个参数：
    
    1）obj表示增强的对象，即实现这个接口类的一个对象；
    
    2）method表示要被拦截的方法；
    
    3）args表示要被拦截方法的参数；
    
    4）proxy表示要触发父类的方法对象；
    
    public Object intercept(Object obj, java.lang.reflect.Method method, Object[] args,MethodProxy proxy) throws Throwable;
    ```


## CGLIB与JDK

* JDK **动态代理类和委托类需要都实现同一个接口**。也就是说只有实现了某个接口的类可以使用Java动态代理机制.
* 但是，事实上使用中并不是遇到的所有类都会给你实现一个接口。因此，**对于没有实现接口的类，就不能使用该机制。而CGLIB则可以实现对类的动态代理**。

