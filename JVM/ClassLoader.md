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

# 类的装载过程/生命周期

* 加载
  
* 通过ClassLoader加载class文件字节码，生成class对象
  
* 链接
  * 校验
    * 检查加载的class的正确性和安全性
  * 准备
    * 为类变量(static)分配存储空间并设置类变量初始值
    * 使用方法区(meta space)内存
  * 解析
    * JVM将常量池内的符号引用转换为直接引用

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



