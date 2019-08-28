# String、StringBuffer、StringBuilder的区别

* 可变与不可变
  * String使用字符数组保存字符串,用final 修饰`private final char value[]`.String对象不可变(指向字符串的指针不可变)
  * StringBuilder与StringBuffer都继承自AbstractStringBuilder类
    * AbstractStringBuilder中也使用字符数组保存字符串`char[] value;`
    * AbstractStringBuilder公共方法
      * expandCapacity
      * append
      * insert
      * indexOf
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

* 安全性考虑
* 支持字符串常量池







