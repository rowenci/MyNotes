# Java集合

二叉树

二叉排序树：结点的左子结点小于结点，右子结点大于结点，中序遍历结果从小到大，顺序排列。

平衡二叉树：该树首先就是一个二叉排序树，任意结点的左、右字数高度差的绝对值不超过1。

B树：多路平衡查找树。所有结点的孩子结点数的最大值成为B树的阶，通常用m表示。一棵m阶B树或为空树，或为满足以下特性的m叉树。

what is Red-Black tree?

Red-Black tree 是平衡二叉搜索树的一种改进树

那么什么是平衡二叉搜索树（AVL）呢？

平衡二叉搜索树是：平衡二叉树+二叉搜索树
1.二叉搜索树：结点大于左孩子，结点小于右孩子中序遍历结果是一个递增的序列
2.平衡二叉树：每个结点的左右子树高度差的绝对值不超过1那么平衡二叉搜索树就是结点大于左孩子，结点小于右孩子每个结点的左右子树高度差的绝对值不超过1因为AVL树的自旋操作，使得元素添加和删除都会浪费时间所以就找个一个限制条件，让该树的添加和删除结点不那么耗时
Red-Black tree是以下性质+二叉搜索树
1.每个结点非黑即红
2.根结点为黑
3.叶结点为红
4.红结点的两个子结点为黑
5.对每个结点，从该结点到其后代所有叶结点的简单路径上，均包含相同数目的黑色结点
6.新结点为红色

综上：Red-Black tree是一个变种的平衡二叉搜索树是为了加速增加和删除结点的一个改进方法是平衡二叉搜索树的折中策略虽然不像平衡二叉树那样，平衡因子始终<=1但是效率也足够高了

![img](https://pics6.baidu.com/feed/a5c27d1ed21b0ef404ef0475c9aca9de81cb3e06.jpeg?token=906fed0b5cbf11bf24b935847e9adb44&s=6B94EC0259CF40CA584D2C680200B073)





<img src="https://img-blog.csdn.net/20160124221843905?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="img"  />

## 1.Collection

### 1.1.Set

这是个乱序存储的一个集合，主要使用它的查找操作，因为类似hash表，查找效率比较高



#### 1.1.1.TreeSet

基于红黑树实现，支持有序性操作，例如根据一个范围查找元素的操作。但是查找效率不如 HashSet，HashSet 查找的时间复杂度为 O(1)，TreeSet 则为 O(logN)。

TreeSet是一个有序的集合，它的作用是提供有序的Set集合。它继承了AbstractSet抽象类，实现了NavigableSet<E>，Cloneable，Serializable接口。TreeSet是基于TreeMap实现的，TreeSet的元素支持2种排序方式：自然排序或者根据提供的Comparator进行排序。

1、不能有重复的元素；
2、具有排序功能；
3、TreeSet中的元素必须实现Comparable接口并重写compareTo()方法，TreeSet判断元素是否重复 、以及确定元素的顺序 靠的都是这个方法；
①对于[Java](http://lib.csdn.net/base/javase)类库中定义的类，TreeSet可以直接对其进行存储，如String，Integer等,因为这些类已经实现了Comparable接口);
②对于自定义类，如果不做适当的处理，TreeSet中只能存储一个该类型的对象实例，否则无法判断是否重复。
4、依赖TreeMap。
5、相对HashSet,TreeSet的优势是有序，劣势是相对读取慢。根据不同的场景选择不同的集合。

https://www.cnblogs.com/LiaHon/p/11221634.html





#### 1.1.2.HashSet

基于哈希表实现，支持快速查找，但不支持有序性操作。并且失去了元素的插入顺序信息，也就是说使用 Iterator 遍历 HashSet 得到的结果是不确定的。

- 它存储唯一元素并允许空值
- 它由HashMap支持
- 它不保持插入顺序
- 它不是线程安全的

```Set<Integer> hs = new HashSet<Integer>();```

https://www.cnblogs.com/LiaHon/p/11257805.html



#### 1.1.3.LinkedHashSet

具有 HashSet 的查找效率，并且内部使用双向链表维护元素的插入顺序。

```Set<Integer> lh = new LinkedHashSet<Integer>();```

https://www.iteye.com/blog/zhangshixi-673319



#### 1.1.4 上面3个集合

TreeSet底层是TreeMap

HashSet底层是HashMap

LinkedHashSet底层是LinkedHashMap

所以可以用类比的方法来学习上面3个集合了。



### 1.2.List

#### 1.2.1.ArrayList

基于动态数组实现，支持随机访问

```List<Integer> arraylist = new ArrayList<Integer>();```

删除元素的时候，如果是最后一个元素，就直接elementData[--size] = null;

否则就对数组重新赋值

https://www.cnblogs.com/LiaHon/p/11089988.html

扩容机制：https://blog.csdn.net/weixin_36378917/article/details/81812210



#### 1.2.2.Vector

和 ArrayList 类似，但它是线程安全的

```List<Integer> vector = new Vector<Integer>();```

```Deque<Integer> stack = new ArrayDeque<Integer>();```

https://baijiahao.baidu.com/s?id=1638844080997170869&wfr=spider&for=pc



#### 1.2.3.LinkedList

基于双向链表实现，只能顺序访问，但是可以快速地在链表中间插入和删除元素。不仅如此，LinkedList 还可以用作栈、队列和双向队列

```List<Integer> ll = new LinkedList<Integer>();```

https://www.cnblogs.com/LiaHon/p/11107245.html



### 1.3.Queue

#### 1.3.1.LinkedList

可以用它来实现双向队列

```Queue<Integer> ll2 = new LinkedList<Integer>();```

https://www.cnblogs.com/yijinqincai/p/10964188.html



#### 1.3.2.PriorityQueue

基于堆结构实现，可以用它来实现优先队列

```Queue<Integer> pq = new PriorityQueue<Integer>();```

https://www.jianshu.com/p/f1fd9b82cb72



## 2.Map

### 2.1.TreeMap

基于红黑树实现

```Map<Integer, Integer> tm = new TreeMap<Integer, Integer>();```

https://www.cnblogs.com/LiaHon/p/11221634.html



### 2.2.HashMap

基于哈希表实现，底层是数组链表

1.7和1.8区别

> 1.底层数据结构不一样，1.7是数组+链表，1.8则是数组+链表+红黑树结构（当链表长度大于8，转为红黑树）。
> 2.JDK1.8中resize()方法在表为空时，创建表；在表不为空时，扩容；而JDK1.7中resize()方法负责扩容，inflateTable()负责创建表。
> 3.1.8中没有区分键为null的情况，而1.7版本中对于键为null的情况调用putForNullKey()方法。但是两个版本中如果键为null，那么调用hash()方法得到的都将是0，所以键为null的元素都始终位于哈希表table【0】中。
> 4.当1.8中的桶中元素处于链表的情况，遍历的同时最后如果没有匹配的，直接将节点添加到链表尾部；而1.7在遍历的同时没有添加数据，而是另外调用了addEntry()方法，将节点添加到链表头部。
> 5.1.7中新增节点采用头插法，1.8中新增节点采用尾插法。这也是为什么1.8不容易出现环型链表的原因。
> 6.1.7中是通过更改hashSeed值修改节点的hash值从而达到rehash时的链表分散，而1.8中键的hash值不会改变，rehash时根据（hash&oldCap）==0将链表分散。
> 7.1.8rehash时保证原链表的顺序，而1.7中rehash时有可能改变链表的顺序（头插法导致）。
> 8.在扩容的时候：1.7在插入数据之前扩容，而1.8插入数据成功之后扩容。

1.7头插法形成循环链表的原因：并发过程下，出现了resize，并且使用了头插法

<img src="https://img2018.cnblogs.com/blog/595137/201901/595137-20190113163240369-1459504789.png" alt="img" style="zoom: 80%;" />

1.8链表转红黑树的时机：链表长度为8，且table元素大于等于64

否则就是扩容，执行resize方法

```Map<Integer, Integer> hm = new HashMap<Integer, Integer>();```

https://www.cnblogs.com/LiaHon/p/11142958.html



为什么负载因子是0.75？

```
<p>As a general rule, the default load factor (.75) offers a good
* tradeoff between time and space costs.  Higher values decrease the
* space overhead but increase the lookup cost (reflected in most of
* the operations of the <tt>HashMap</tt> class, including
* <tt>get</tt> and <tt>put</tt>).  The expected number of entries in
* the map and its load factor should be taken into account when
* setting its initial capacity, so as to minimize the number of
* rehash operations.  If the initial capacity is greater than the
* maximum number of entries divided by the load factor, no rehash
* operations will ever occur.

一般情况下，默认的负载因子（.75）在时间和空间之间提供了一个不错的平衡。
更高的值会减少没有用到的空间，但是增加了查询的消耗（反映在HashMap当中的大多数操作，包括get和put）。
在map当中元素的期望数值和它的负载因子应该在设置初始容量的时候就被考虑到，让rehash操作的次数降到最小也是。
如果初始容量比最大元素个数除以负载因子都大，那么rehash操作就永远不会发生了。
```

```java
	/**
     * The default initial capacity - MUST be a power of two.
     */
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16

    /**
     * The maximum capacity, used if a higher value is implicitly specified
     * by either of the constructors with arguments.
     * MUST be a power of two <= 1<<30.
     */
    static final int MAXIMUM_CAPACITY = 1 << 30; // 10亿+

    /**
     * The load factor used when none specified in constructor.
     */
    static final float DEFAULT_LOAD_FACTOR = 0.75f;

    /**
     * The bin count threshold for using a tree rather than list for a
     * bin.  Bins are converted to trees when adding an element to a
     * bin with at least this many nodes. The value must be greater
     * than 2 and should be at least 8 to mesh with assumptions in
     * tree removal about conversion back to plain bins upon
     * shrinkage.
     */
    static final int TREEIFY_THRESHOLD = 8;

    /**
     * The bin count threshold for untreeifying a (split) bin during a
     * resize operation. Should be less than TREEIFY_THRESHOLD, and at
     * most 6 to mesh with shrinkage detection under removal.
     */
    static final int UNTREEIFY_THRESHOLD = 6;

    /**
     * The smallest table capacity for which bins may be treeified.
     * (Otherwise the table is resized if too many nodes in a bin.)
     * Should be at least 4 * TREEIFY_THRESHOLD to avoid conflicts
     * between resizing and treeification thresholds.
     */
    static final int MIN_TREEIFY_CAPACITY = 64;
```

键值均可为null



### 2.3.HashTable

和 HashMap 类似，但它是线程安全的，这意味着同一时刻多个线程同时写入 HashTable 不会导致数据不一致。
它是遗留类，不应该去使用它，而是使用 ConcurrentHashMap 来支持线程安全，
ConcurrentHashMap 的效率会更高，因为 ConcurrentHashMap 引入了分段锁。

底层没有红黑树

```Map<Integer, Integer> ht = new Hashtable<Integer, Integer>();```

[https://blog.csdn.net/qq_38293564/article/details/80693750?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522159108935619725219927760%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=159108935619725219927760&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~top_click~default-3-80693750.pc_v1_rank_blog_v1&utm_term=hashtable](https://blog.csdn.net/qq_38293564/article/details/80693750?ops_request_misc=%7B%22request%5Fid%22%3A%22159108935619725219927760%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=159108935619725219927760&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~top_click~default-3-80693750.pc_v1_rank_blog_v1&utm_term=hashtable)

键值均不可为空



### 2.4.LinkedHashMap

继承HashMap，多了一个双向链表，直接在元素上维护的

使用双向链表来维护元素的顺序，顺序为插入顺序或者最近最少使用（LRU）顺序

```Map<Integer, Integer> lhm = new LinkedHashMap<Integer, Integer>();```

https://blog.csdn.net/wang_8101/article/details/89111314



## ConcurrentHashMap

https://www.jianshu.com/p/865c813f2726

https://www.jianshu.com/p/d0b37b927c48

https://www.bilibili.com/video/BV1UC4y1H7Ri?p=2