# 谈谈ClassLoader

* ClassLoader在Java中有着非常重要的作用，它主要工作在Class装载的加载阶段，其主要作用是从系统外部获得Class二进制数据流。

* 它是Java的核心组件，所有的Class都是由ClassLoader进行加载的，ClassLoader负责通过将Class文件里的二进制数据流装载进系统，然后交给Java虚拟机进行连接，初始化等操作。

# ClassLoader的种类

* 启动类加载器`BootStrapClassLoader`
  * C++编写，加载核心库 java.*
* 扩展类加载器`ExtClassLoader`
  * Java编写，加载扩展库javax.*
* 应用程序类加载器`AppClassLoader`
  * Java编写，加载程序所在目录
* 自定义ClassLoader
  * Java编写，定制化加载



# 双亲委派机制加载类

* 避免多份同样字节码的加载

* 工作过程 

  * 类加载器将类加载请求转发到父类加载器,父类无法完成时自己加载

  

# 类加载方式

* 隐匿加载
  * new
* 显式加载
  * loadClass
  * forName

# [类的加载过程/生命周期](https://zhuanlan.zhihu.com/p/33509426)

![img](https://i.loli.net/2020/08/01/CD1TkAag5B2wQGi.jpg)

* 加载
  
  * 通过ClassLoader加载class文件字节码，生成class对象
  
* 链接
  * 校验
    
    * 检查加载的class的正确性和安全性
  * 准备
    * 主要是为类变量（注意，不是实例变量）分配内存，并且赋予**初值**。
    
      特别需要注意，**初值，不是代码中具体写的初始化的值**，而是Java虚拟机根据不同变量类型的默认初始值。
  
      比如8种**基本类型**的初值，默认为0；**引用类型**的初值则为null；**常量**的初值即为代码中设置的值，final static tmp = 456， 那么该阶段tmp的初值就是456
  * 解析
    
    * JVM将常量池内的符号引用转换为直接引用
      * **符号引用**。即一个字符串，但是这个字符串给出了一些能够唯一性识别一个方法，一个变量，一个类的相关信息。
      * **直接引用**。可以理解为一个内存地址，或者一个偏移量。比如**类方法，类变量**的直接引用是指向方法区的**指针**；而**实例方法，实例变量**的直接引用则是从实例的头指针开始算起到这个实例变量位置的**偏移量**
  
* 初始化

  * 在准备阶段,类变量已被赋过系统默认值,该阶段根据程序赋值

  * 时机 

    * 主动引用 

      * `new` `getstatic` `putstatic` `invokestatic`
      * 反射调用且类没被初始化
      * 初始化类时,父类未初始化
      * 虚拟机启动时会初始化一个主类(main)

    * 被动引用 (被动引用不触发初始化)

      * 子类引用父类静态字段

      * 数组定义引用类,会对数组类初始化

      * 常量类不会初始化

        

* 使用

* 卸载

# loadClass和forName区别

* Classloader.loadClass(className) 
  * 目标对象被装载后不会进行链接，loadClass不会执行静态代码块
* Class.forName(className)
  * 在loadClass之后须初始化



