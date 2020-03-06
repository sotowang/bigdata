# 异常类型

* 受检查的异常(checked)
  * 需要用throws语句在方法或构造函数上声明
* 不受检查的异常（unchecked）
  * 不需在方法或构造函数上声明
  * 可传播到方法或构造函数外面

# 异常机制

- 运行时异常（unchecked）
  - 由JVM接管,比如空指针异常
- 检查式异常（checked exception）
  - IO异常及SQL异常
  - 需要catch,不管我们是否愿意,都要写

# throw和throws区别

* throw关键字用来在程序中明确地抛出异常
* throws来表明方法不能处理的异常，多个异常用逗号分隔

# Throwable

* 是所有错误和异常的超类
* 2个子类
  * Error
  * Exception

## Error和Exception的区别

* 父类都为Throwable
* Error：程序无法处理的系统错误，编译器不做检查,JVM承担的责任,大部分会终止线程
* Exception：程序可以处理的异常，捕获后可能恢复
  * unCheckedException
    * 不可预知的，程序应当自行避免，由程序承担责任
      * 空指针异常，数组下标越界
  * CheckedException可检查异常是Java编译器应该负担的责任

# RuntimeException

* NullPointerException -空指针引用异常
* ClassCastException -类型强制转换异常
* IllegalArgumentException - 传递非法参数异常
* IndexOutOfBoundsException - 下标越界异常
* NumberFormatException - 数字格式异常 

# Checked Exception

* ClassNotFoundException - 找不到指定class异常
* IOException - IO操作异常

# Error

* `NoClassDefFoundError`
  * 类依赖的class或者jar不存在
  * 类文件存在，但是存在不同的域中
  * 大小写问题，javac编译的时候是无视大小写的，很有可能编译出的class文件与想要的不一样
* `StackOverflowError` 
* `OutOfMemoryError` 
  * `Java heap space`
  * `GC overhead limit exceeded`
    * 超过98%的时间用来做GC并且回收不到2%内存
  * `Direct buffer memory`
    * 堆内存充足，本地内存用尽；因堆内存使用少，不需要GC，`DirectByteBuffer`对象就不会被回收
  * `unable to create new native thread`
    * 原因
      * 一个应用进程创建多个线程，超过系统极限（Linux默认1024）
    * 解决
      * 降低自身应用线程数
      * 修改linux服务器配置
  * `Metaspace`

# Java的异常处理机制

* 抛出异常
  * 创建异常对象，交由运行时系统处理
* 捕获异常
  * 寻找合适的异常处理器处理异常，否则终止运行

# 异常处理框架

在用户看来，应用系统发生的所有异常都是应用系统内部的异常

* 设计一个通用的继承自RuntimeException的异常来统一处理
* 其余异常都统一转译为上述异常AppException
* 在catch之后，抛出上述异常的子类，并提供足以定位的信息
* 由前端接收AppException做统一处理

