##### List，Set，Map

- list和set有共同的父类，他们的用法也是一样的，唯一的不同是set中不能有相同的元素，list中可以。
- list和set的用途非常广泛，list可以完全代替数组使用
- map是独立的合集，他使用键值对的方式来储存数据，键不能有重复的，值可以
- map不像上面那两个集合用的广泛，不过在servlet和jsp中，map是重中之重，页面传值全靠map

***

###### List，Set，Map的子类

```java
Collection
├List
│├LinkedList
│├ArrayList
│└Vector
│　└Stack
└Set
 |-HashSet
 └TreeSet      
    
Map
├Hashtable
├HashMap
└WeakHashMap
```

***

##### List

- LinkedList（双向链表）

  - linkedlist实现list接口，允许null元素。此外，linkedlist提供额外的get，remove，insert方法在linkedlist的首部或尾部。这些操作使linkedlist可被用作堆栈，队列，或双向队列

  - linkedlist没有同步方法。如果多个线程同时访问一个list，则必须自己实现同步访问。例：创建linkedlist时构造一个同步的list

    ```java
    List list = Collections.synchronizedList(new LinkedList(…));
    ```

  - 特点：寻址困难，插入和删除容易。

- ArrayList

  - arraylist实现了可变大小的数组。他允许所有的元素，包括null。arraylist没有同步。
  - size，isEmpty，get，set方法运行时间为常数。但是add方法开销为分摊的常数，添加n个元素需要O(n)的时间。其他的方法运行时间为线性。
  - 每个arraylist实例都有一个容量（capacity），即用于存储元素的数组的大小。这个容量可随着不断添加新元素而自动增加，但增长算法并没有定义。当需要插入大量元素的时候，在插入前可以调用ensureCapacity方法来增加ArrayList的容量以提高插入效率。
  - 和LinkedList一样，ArrayList也是非同步的（unsynchronized）。
  - 特点：寻址容易，插入和删除困难。

  

##### Set

- HashSet
  - 他不允许出现重复元素。
  - 不保证集合中元素的顺序。
  - 允许null元素，但是最多只能有一个null元素。
  - HashSet的实现是不同步的。

- TreeSet
  - 实现Set接口，该接口由TreeMap实例支持。此类保证排序后的set按照升序排列元素，根据使用的构造方法不同，可能会按照元素的自然顺序进行排序，或者在创建Set时所提供的比较器进行排序。
  - treeset描述的是set的一种变体—可以实现排序等功能的集合，他在将对象元素添加到集合中时会自动按照某种比较规则将其插入到有序的对象序列中。
  - HashSet是基于Hash算法实现的，其性能通常优于TreeSet，我们通常都应该使用HashSet，在我们需要排序的时候，我们才使用TreeSet。

##### Map*

- HashTable
  - hashtable继承Map类，实现一个key-value映射的哈希表，任何非空的对象都可以作为Key或者value。添加数据使用put（key，value），取出数据使用get（key），这两个基本操作的时间开销为常数。
  - Hashtable 通过initial capacity和load factor两个参数调整性能。通常缺省的load factor 0.75较好地实现了时间和空间的均衡。增大load factor可以节省空间但相应的查找时间将增大，这会影响像get和put这样的操作。
  - 作为key的对象将通过计算其散列函数来确定与之对应的value的位置，因此任何作为key的对象都必须实现hashCode和equals方法。
  - Hashtable是同步的。

- HashMap

  - hashmap和hashtable类似，不同之处在于hashmap是非同步的，并且允许null，即null key和null value。其迭代子操作时间开销和HashMap 的容量成比例,因此，不要将HashMap的初始化容量设得过高，或者load factor过低。

  - HashMap的遍历方式

    - ~~~
          Map map = new HashMap();
          map.put("Key1",1);
          Set s = map.entrySet();
          Iterator iterator = s.iterator();
          while (iterator.hasNext()){
              Map.Entry<String,Integer> entry = (Map.Entry<String, Integer>) iterator.next();
              System.out.println(entry.getKey());
              System.out.println(entry.getValue());
          }
      ~~~

  - HashMap的数据结构

    - HashMap里面实现一个静态内部类Entry，其重要的属性有Key，Value，Next

    - value的值是元素的key的哈希值对数组长度取模得到的。如下面第二幅图中，12%16=12,28%16=12,108%16=12,140%16=12。所以12、28、108以及140都存储在数组下标为12的位置。

      ![preview](https://pic3.zhimg.com/v2-2e833fa90f58c493a3e5a0d122ea143e_r.jpg)

    ![preview](https://pic4.zhimg.com/v2-2f359d95bb4e4013eaf1185372e99807_r.jpg)

    - HashMap里面用到链式数据结构的一个概念。上面我们提到过Entry类里面有一个next属性，作用是指向下一个Entry。打个比方， 第一个键值对A进来，通过计算其key的hash得到的index=0，记做:Entry[0] = A。一会后又进来一个键值对B，通过计算其index也等于0，现在怎么办？HashMap会这样做:B.next = A,Entry[0] = B,如果又进来C,index也等于0,那么C.next = B,Entry[0] = C；这样我们发现index=0的地方其实存取了A,B,C三个键值对,他们通过next这个属性链接在一起。所以疑问不用担心。也就是说数组中存储的是最后插入的元素。到这里为止，HashMap的大致实现，我们应该已经清楚了。

