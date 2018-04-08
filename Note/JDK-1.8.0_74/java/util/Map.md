# 基本概念
Map是Key-Value对映射的抽象接口。该映射不包括重复的键，即一个键对应一个值。

这个接口替代了Dictionary这个类,Dictionary是抽象类而非接口.

Map接口提供了3个集合视图,包括:keys的set集合; values的集合; key-value的set集合.(注意:values集合不是set类型,因为value可相同)

相关重要的类：
+ AbstractMap：实现了Map接口的抽象类。Map的基本实现，其他Map的实现类可以通过继承AbstractMap来减少编码量。
+ SortedMap：继承Map。保证按照键的升序排列的映射，对entrySet、keySet和values方法返回的结果进行迭代时，顺序就会反映出来。
+ NavigableMap：继承SortedMap，含有返回特定条件最近匹配的导航方法。
+ HashMap：Map接口基于哈希表的实现，是使用频率最高的用于键值对处理的数据类型。它根据键的hashCode值存储数据，大多数情况下可以直接定位到它的值，特点是访问速度快，遍历顺序不确定，线程不安全，最多允许一个key为null，允许多个value为null。可以用 Collections的synchronizedMap方法使HashMap具有线程安全的能力，或者使用ConcurrentHashMap类。
+ HashTable：Hashtable和HashMap从存储结构和实现来讲有很多相似之处，不同的是它承自Dictionary类，而且是线程安全的，另外Hashtable不允许key和value为null。并发性不如ConcurrentHashMap，因为ConcurrentHashMap引入了分段锁。Hashtable不建议在新代码中使用，不需要线程安全的场合可以使用HashMap，需要线程安全的场合可以使用ConcurrentHashMap。
+ LinkedHashMap： LinkedHashMap继承了HashMap，是Map接口的哈希表和链接列表实现。它维护着一个双重链接列表。此链接列表定义了迭代顺序，该迭代顺序可以是插入顺序或者是访问顺序。
+ WeakedHashMap： 以弱键实现的基于哈希表的Map。在WeakHashMap中，当某个键不再正常使用时，将自动移除其条目。
+ TreeMap ： Map接口基于红黑树的实现。

# 主要方法及内部类
## 方法
+ void clear(); 清空所有键值对
+ compute
+ computeIfAbsent
+ computeIfPresent
+ boolean containsKey(Object key); 如果map存储了指定的key,则返回true.更一般的情况是,当且仅当map包含了一个key的映射: 映射情况是:key==null ? k==null : key.equals(k),此时返回true.
+ boolean containsValue(Object value); 如果map中至少有一个key能映射到指定的value,那么就返回true.更一般的情况是,当且仅当value==null ? v==null : value.equals(v)。
+ entrySet
+ equals
+ forEach
+ V get(Object key); 返回指定key映射的value.如果map没有指定的key,则返回null.
+ getOrDefault
+ hashCode
+ boolean isEmpty(); 如果map没有存储任何key-value,则返回true.
+ Set<K> keySet(); 返回map包含所有的key的一个set集合视图.
+ merge
+ V put(K key, V value); put方法是将指定的key-value存储到map里面的操作.如果map之前包含了一个此key对应的映射,那么此key对应的旧value值会被新的value值替换.
+ void putAll(Map<? extends K, ? extends V> m); putAll方法是将一个指定map的映射拷贝到当前map.
+ putIfAbsent
+ V remove(Object key); remove方法用于移除map中已有的某个key.本方法会返回移除的key对应的value值,如果map这个key没有对应的value值,则返回null.
+ remove
+ replace
+ replace
+ replaceAll
+ int size(); 返回map中key-value映射的个数.如果map包含的key-value个数超过了Integer.MAX_VALUE这个数, 则返回Integer.MAX_VALUE.
+ values


## 内部类
Entry