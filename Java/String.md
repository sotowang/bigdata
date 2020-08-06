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
  * StringBuffer修改时会修改对象自身

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

# [关于String创建几个对象](https://juejin.im/post/6844904129752465416)

## `String s1 = new String("Java");`

  * 可能创建 1 个或 2 个对象，new String 会先去常量池中判断有没有此字符串，如果有则只在堆上创建一个字符串并且指向常量池中的字符串，如果常量池中没有此字符串，则会创建 2 个对象，先在常量池中新建此字符串，然后把此引用返回给堆上的对象，如下图所示：

    ![new 字符串常量池.png](https://user-gold-cdn.xitu.io/2020/4/17/17185b71e45e6c16?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

## [`String a="a"+"b"+"c"`](https://www.zhihu.com/question/38733755)

* 通过编译器优化后，得到的效果是

```text
String a="abc"
```

* 如果字符串常量池中存在abc，则该语句并不会创建对象，只是讲字符串常量池中的引用返回而已。
* 如果字符串常量池中不存在abc，则会创建并放入字符串常量池，并返回引用，此时会有一个对象进行创建。