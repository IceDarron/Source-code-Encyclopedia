# 基本概念
CocurrentHashMap利用锁分段技术增加了锁的数目，从而使争夺同一把锁的线程的数目得到控制。 

锁分段技术就是对数据集进行分段，每段竞争一把锁，不同数据段的数据不存在锁竞争，从而有效提高 高并发访问效率。

CocurrentHashMap在get方法是无需加锁的，因为用到的共享变量都采用volatile关键字修饰，共享变量在线程之间的可见性(每次读取都先同步缓存和内存，
直接从内存中获取值，虽然不是原子操作，但根据JAVA内存模型的happen before原则，对volatile字段的写入操作先于读操作，
能够保证不会脏读),volatile为了让变量提供线程之间的内存可见性，会禁止程序执行结果的重排序（导致缓存优化的效果降低）.

+ JDK1.8的ConcurrentHashMap中Segment虽保留，但已经简化属性，仅仅是为了兼容旧版本。

+ ConcurrentHashMap的底层与Java1.8的HashMap有相通之处，底层依然由“数组”+链表+红黑树来实现的，底层结构存放的是TreeBin对象，而不是TreeNode对象；

+ ConcurrentHashMap实现中借用了较多的CAS算法，unsafe.compareAndSwapInt(this, valueOffset, expect, update); CAS(Compare And Swap)，意思是如果valueOffset位置包含的值与expect值相同，则更新valueOffset位置的值为update，并返回true，否则不更新，返回false。

+ ConcurrentHashMap既然借助了CAS来实现非阻塞的无锁实现线程安全，那么是不是就没有用锁了呢？？答案：还是使用了synchronized关键字进行同步了的，在哪里使用了呢？在操作hash值相同的链表的头结点还是会synchronized上锁，这样才能保证线程安全。

```java
public class ConcurrentHashMap<K,V> extends AbstractMap<K,V>
    implements ConcurrentMap<K,V>, Serializable {
    //...
}
```

### 基本属性
+ private transient volatile int sizeCtl;//控制标识符 负数代表正在进行初始化或扩容操作 ,其中-1代表正在初始化 ,-N 表示有N-1个线程正在进行扩容操作。正数或0代表hash表还没有被初始化，这个数值表示初始化或下一次进行扩容的大小，类似于扩容阈值。它的值始终是当前ConcurrentHashMap容量的0.75倍，这与loadfactor是对应的。实际容量>=sizeCtl，则扩容。
+ transient volatile Node<K,V>[] table;是一个容器数组，第一次插入数据的时候初始化，大小是2的幂次方。这就是我们所说的底层结构：”数组+链表（或树）”
+ private static final int MAXIMUM_CAPACITY = 1 << 30; // 最大容量
+ private static final intDEFAULT_CAPACITY = 16;
+ static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8; // MAX_VALUE=2^31-1=2147483647
+ private static finalint DEFAULT_CONCURRENCY_LEVEL = 16;
+ private static final float LOAD_FACTOR = 0.75f;
+ static final int TREEIFY_THRESHOLD = 8; // 链表转树的阀值，如果table[i]下面的链表长度大于8时就转化为数
+ static final int UNTREEIFY_THRESHOLD = 6; //树转链表的阀值，小于等于6是转为链表，仅在扩容tranfer时才可能树转链表
+ static final int MIN_TREEIFY_CAPACITY = 64;
+ private static final int MIN_TRANSFER_STRIDE = 16;
+ private static int RESIZE_STAMP_BITS = 16;
+ private static final int MAX_RESIZERS = (1 << (32 - RESIZE_STAMP_BITS)) - 1; // help resize的最大线程数
+ private static final int RESIZE_STAMP_SHIFT = 32 - RESIZE_STAMP_BITS;
+ static final int MOVED = -1; // hash for forwarding nodes（forwarding nodes的hash值）、标示位
+ static final int TREEBIN = -2; // hash for roots of trees（树根节点的hash值）
+ static final int RESERVED = -3; // hash for transient reservations（ReservationNode的hash值）

### 主要方法
```java
public class ConcurrentHashMap<K,V> extends AbstractMap<K,V>
    implements ConcurrentMap<K,V>, Serializable {
    public V put(K key, V value) {
            return putVal(key, value, false);
        }
}
/**
 * putVal(K key, V value, boolean onlyIfAbsent)方法干的工作如下：
 * 1、检查key/value是否为空，如果为空，则抛异常，否则进行2
 * 2、进入for死循环，进行3
 * 3、检查table是否初始化了，如果没有，则调用initTable()进行初始化然后进行 2，否则进行4
 * 4、根据key的hash值计算出其应该在table中储存的位置i，取出table[i]的节点用f表示。
 *     根据f的不同有如下三种情况：1）如果table[i]==null(即该位置的节点为空，没有发生碰撞)，
 *                                 则利用CAS操作直接存储在该位置，如果CAS操作成功则退出死循环。
 *                                 2）如果table[i]!=null(即该位置已经有其它节点，发生碰撞)，碰撞处理也有两种情况
 *                                     2.1）检查table[i]的节点的hash是否等于MOVED，如果等于，则检测到正在扩容，则帮助其扩容
 *                                     2.2）说明table[i]的节点的hash值不等于MOVED，如果table[i]为链表节点，则将此节点插入链表中即可
 *                                          如果table[i]为树节点，则将此节点插入树中即可。插入成功后，进行 5
 * 5、如果table[i]的节点是链表节点，则检查table的第i个位置的链表是否需要转化为数，如果需要则调用treeifyBin函数进行转化
 */
```

```java
public class ConcurrentHashMap<K,V> extends AbstractMap<K,V>
    implements ConcurrentMap<K,V>, Serializable {
/*
     功能：根据key在Map中找出其对应的value，如果不存在key，则返回null，
     其中key不允许为null，否则抛异常
     */
    public V get(Object key) {
        Node<K,V>[] tab; Node<K,V> e, p; int n, eh; K ek;
        int h = spread(key.hashCode());//两次hash计算出hash值
        if ((tab = table) != null && (n = tab.length) > 0 &&//table不能为null，是吧
            (e = tabAt(tab, (n - 1) & h)) != null) {//table[i]不能为空，是吧
            if ((eh = e.hash) == h) {//检查头结点
                if ((ek = e.key) == key || (ek != null && key.equals(ek)))
                    return e.val;
            }
            else if (eh < 0)//table[i]为一颗树
                return (p = e.find(h, key)) != null ? p.val : null;
            while ((e = e.next) != null) {//链表，遍历寻找即可
                if (e.hash == h &&
                    ((ek = e.key) == key || (ek != null && key.equals(ek))))
                    return e.val;
            }
        }
        return null;
    }
}
/**
 * 1、根据key调用spread计算hash值；并根据计算出来的hash值计算出该key在table出现的位置i.
 * 2、检查table是否为空；如果为空，返回null，否则进行3
 * 3、检查table[i]处桶位不为空；如果为空，则返回null，否则进行4
 * 4、先检查table[i]的头结点的key是否满足条件，是则返回头结点的value；否则分别根据树、链表查询。
 */
```



### 参考资料
https://blog.csdn.net/u010412719/article/details/52145145