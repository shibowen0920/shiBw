##### String,StringBuffer,StringBuilder的区别

| String                                                       | StringBuffer                                                 | StringBuilder    |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ---------------- |
| String的值是不可变的，这就导致每次对String的操作都会生成新的String对象，不仅效率低下，而且浪费大量优先的内存空间 | StringBuffer是可变类，和线程安全的字符串操作类，任何对它指向的字符串的操作都不会产生新的对象。每个StringBuffer对象都有一定的缓冲区容量，当字符串大小没有超过容量时，不会分配新的容量，当字符串大小超过容量时，会自动增加容量 | 可变类，速度更快 |
| 不可变                                                       | 可变                                                         | 可变             |
|                                                              | 线程安全                                                     | 线程不安全       |
|                                                              | 多线程操作字符串                                             | 单线程操作字符串 |

- String类 - 字符串常量

  String和StringBuffer类型的主要性能区别在于String是不可变的对象，因此在每次对String类型进行改变的时候其实都等同于**生成了一个新的String对象**，然后将指针指向了新的String对象，这样不仅效率低下，而且大量的浪费有限的内存空间。所以经常改变内容的字符串最好不用String。因为每次生成对象都会对系统性能产生影响，特别当内存中无引用对象多了以后， JVM 的 GC 就会开始工作，那速度是一定会相当慢的。

  ![img](https://img-blog.csdn.net/20180411091757991?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTEwMTE3Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

- StringBuffer和StringBuilder类

  - StringBuffer - 线程安全

  - StringBuilder - 线程不安全

  - 当**对字符串进行修改的时候，特别是字符串对象经常改变的情况下**，需要使用 StringBuffer 和 StringBuilder 类。

    和 String 类不同的是，StringBuffer 和 StringBuilder 类的对象能够被多次的修改，并且**不产生新的未使用对象**。

    StringBuilder 类在 Java 5 中被提出，它和 StringBuffer 之间的最大不同在于 StringBuilder 的方法不是线程安全的（不能同步访问）。

    由于 StringBuilder 相较于 StringBuffer 有速度优势，**所以多数情况下建议使用 StringBuilder 类**。然而在应用程序要求线程安全的情况下，则必须使用 StringBuffer 类。

- 总结
  - 如果操作少量字符串 - String
  - 多线程操作字符串缓冲区下操作大量数据 - StringBuffer
  - 单线程操作字符串缓冲区下操作大量数据 - StringBuilder