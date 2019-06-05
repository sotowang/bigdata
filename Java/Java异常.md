# Error和Exception的区别

* Error：程序无法处理的系统错误，编译器不做检查
* Exception：程序可以处理的异常，捕获后可能恢复
* 总结：前者是程序无法处理的错误，后者是可以处理的异常

**RuntimeException**

* 不可预知的，程序应当自行避免
  * 空指针异常，数组下标越界

**非RuntimeException**

**从责任角度看**

* Error属于JVM需要负担的责任
* RuntimeException是程序应该负担的责任
* CheckedException可检查异常是Java编译器应该负担的责任

# 常见Error以及Exception

## RuntimeException

* NullPointerException -空指针引用异常
* ClassCastException -类型强制转换异常
* IllegalArgumentException - 传递非法参数异常
* IndexOutOfBoundsException - 下标越界异常
* NumberFormatException - 数字格式异常 

## 非RuntimeException

* ClassNotFoundException - 找不到指定class异常
* IOException - IO操作异常

# Error

* NoClassDefFoundError
  * 类依赖的class或者jar不存在
  * 类文件存在，但是存在不同的域中
  * 大小写问题，javac编译的时候是无视大小写的，很有可能编译出的class文件与想要的不一样
* StackOverflowError - 深递归导致栈被耗尽而抛出的异常
* OutOfMemoryError - 内存溢出异常



