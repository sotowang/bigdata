# String、StringBuffer、StringBuilder的区别

* 可变与不可变
  * String使用字符数组保存字符串,用final 修饰`private final char value[]`.String对象不可变(指向字符串的指针不可变)
    * Java1.9之后使用`private final byte[] value`
  * StringBuilder与StringBuffer都继承自AbstractStringBuilder类
    * AbstractStringBuilder中也使用字符数组保存字符串`char[] value;`
    * AbstractStringBuilder公共方法
      * expandCapacity
      * append
      * insert
      * indexOf
  * 都是final类,不允许被继承
* 线程安全
  * String对象不可变.线程安全
  * StringBuffer对方法加了同步锁或者调用的方法加了同步锁.线程安全
  * StringBuilder非线程安全
* StringBuffer与StringBuilder共同点
  * 公共父类AbstractStringBuilder
  * 有公共方法,StringBuffer会在方法上加Synchronized关键字同步
* 如果程序不是多线程,StringBuilder效率更高
* String与StringBuffer的区别
  * String修改时等同于创建一个新的String对象,经常改变字符内容最好不要用String
  * StringBuffer修改时会以身试法对象自身

# String类为什么要设计成final

* 可以缓存hash值
  * String的hash值经常被使用,如String用做HashMap的key,只需计算 一次
* 常量池需要
  * 若String对象已被创建过,会从常量池取得引用,只有不可变才能使用常量池
* 安全性

# String不变性的理解

* String被final修饰,无法被继承
* 用+连接字符串时会创建新的字符串
* `String s = new String(“Hello world”)`可能创建两个对象,也可能创建一个
  * 若常量池中有"Hello world"字符串常量对象,则仅仅在堆中创建一个对象
  * 若常量池中没有"Hello world"对象,则堆上和常量池中都要创建对象
* 通过使用`+`连接字符串时,实际上底层会转成通过`StringBuilder`实例的`append()`方法来实现

# String Pool

* 保存着所有字符串字面量,可通过`intern()`方法将字符串添加入常量池
* 当字符串调用`intern()`方法
  * 若String pool已存在该值,则返回String Pool中引用
  * 若不存在池中,则添加并返回池中引用

# final好处

* 提高了性能,JVM和Java应用都会缓存final变量
* 可以安全地在多线程环境下进行共享,而不需要额外的同步开销
* 使用final关键字,JVM会对方法,变量及类进行优化

