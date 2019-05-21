# 平时项目中使用锁和synchronized比较多，而很少使用volatile，难道就没有保证可见性？

答：锁和synchronized即可以保证原子性，也可以保证可见性。都是通过保证同一时间只有一个线程执行目标代码段来实现的。

# 锁和synchronized为何能保证可见性？

答：根据[JDK 7的Java doc](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/package-summary.html#MemoryVisibility)中对`concurrent`包的说明，一个线程的写结果保证对另外线程的读操作可见，只要该写操作可以由`happen-before`原则推断出在读操作之前发生。

> The results of a write by one thread are guaranteed to be **visible** to a read by another thread only if the write operation happens-before the read operation. The synchronized and volatile constructs, as well as the Thread.start() and Thread.join() methods, can form happens-before relationships.

# 既然锁和synchronized即可保证原子性也可保证可见性，为何还需要volatile？

答：**synchronized和锁需要通过操作系统来仲裁谁获得锁**，开销比较高，而volatile开销小很多。因此在只需要保证可见性的条件下，使用volatile的性能要比使用锁和synchronized高得多。

# 既然锁和synchronized可以保证原子性，为什么还需要AtomicInteger这种的类来保证原子操作？

答：锁和synchronized需要通过操作系统来仲裁谁获得锁，开销比较高，而AtomicInteger是通过CPU级的CAS操作来保证原子性，开销比较小。所以使用AtomicInteger的目的还是为了提高性能。

# 还有没有别的办法保证线程安全

答：有。尽可能避免引起非线程安全的条件——**共享变量**。如果能从设计上避免共享变量的使用，即可避免非线程安全的发生，也就无须通过锁或者synchronized以及volatile解决原子性、可见性和顺序性的问题。

# synchronized即可修饰非静态方式，也可修饰静态方法，还可修饰代码块，有何区别

答：synchronized修饰非静态同步方法时，**锁住的是当前实例**；synchronized修饰静态同步方法时，**锁住的是该类的Class对象**；synchronized修饰静态代码块时，**锁住的是`synchronized`关键字后面括号内的对象**。

 