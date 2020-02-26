# [Java 动态代理作用是什么？](https://www.zhihu.com/question/20794107)

# Java动态代理实现的两种方法

## JDK动态代理

* 由Java内部反射机制实现，在生成类的过程中比较高效
* 目标类基于统一接口，否则不能使用
* 目标类方法都要在接口中声明

## CGLIB动态代理

* 借助asm来实现，在生成类之后的相关执行过程中比较高效（可通过将asm生成的类进行缓存解决asm生成类过程低效问题）

* 无法代理被final修饰的方法

* 必须实现MethodInterceptor(方法拦截器)接口

  * ```java
    //这个接口只有一个intercept()方法，这个方法有4个参数：
    
    1）obj表示增强的对象，即实现这个接口类的一个对象；
    
    2）method表示要被拦截的方法；
    
    3）args表示要被拦截方法的参数；
    
    4）proxy表示要触发父类的方法对象；
    
    public Object intercept(Object obj, java.lang.reflect.Method method, Object[] args,MethodProxy proxy) throws Throwable;
    ```

    

