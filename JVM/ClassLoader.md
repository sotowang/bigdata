# ClassLoader的种类

* BootStrapClassLoader
  * C++编写，加载核心库 java.*
* ExtClassLoader
  * Java编写，加载扩展库javax.*
* AppClassLoader
  * Java编写，加载程序所在目录
* 自定义ClassLoader
  * Java编写，定制化加载

# 谈谈ClassLoader

ClassLoader在Java中有着非常重要的作用，它主要工作在Class装载的加载阶段，其主要作用是从系统外部获得Class二进制数据流。它是Java的核心组件，所有的Class都是由ClassLoader进行加载的，ClassLoader负责通过将Class文件里的二进制数据流装载进系统，然后交组Java虚拟机进行连接，初始化等操作。

# 为什么要使用双亲委派机制加载类

* 避免多份同样字节码的加载

  

# 类加载方式

* 隐匿加载
  * new
* 显式加载
  * loadClass
  * forName

# 类的装载过程

* 加载
  * 通过ClassLoader加载class文件字节码，生成class对象
* 链接
  * 校验
    * 检查加载的class的正确性和安全性
  * 准备
    * 为类变量分配存储空间并设置类变量初始值
  * 解析
    * JVM将常量池内的符号引用转换为直接引用
* 初始化

# loadClass和forName区别

* Classloader.loadClass(className) 
  * 目标对象被装载后不会进行链接，loadClass不会执行静态代码块
* Class.forName(className)
  * 在loadClass之后须初始化



