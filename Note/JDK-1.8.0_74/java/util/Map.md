# 基本概念
Map是Key-Value对映射的抽象接口。该映射不包括重复的键，即一个键对应一个值。

这个接口替代了Dictionary这个类,Dictionary是抽象类而非接口.

Map接口提供了3个集合视图,包括:keys的set集合; values的集合; key-value的set集合.(注意:values集合不是set类型,因为value可相同)

主要有四个实现类：
+ HashTable 线程安全，哈希表。效率低（因为元素修改的方法都加了synchronized）。本质是HashMap。
+ HashMap 线程不安全。哈希表（桶，分治思想）。
+ LinkedHashMap 线程不安全。哈希表，链表结构。
+ TreeMap 线程不安全。红黑树结构。

# 主要方法及内部类
## 方法
void clear(); 清空所有键值对
compute
computeIfAbsent
computeIfPresent
boolean containsKey(Object key); 如果map存储了指定的key,则返回true.更一般的情况是,当且仅当map包含了一个key的映射: 映射情况是:key==null ? k==null : key.equals(k),此时返回true.
boolean containsValue(Object value); 如果map中至少有一个key能映射到指定的value,那么就返回true.更一般的情况是,当且仅当value==null ? v==null : value.equals(v)。
entrySet
equals
forEach
V get(Object key); 返回指定key映射的value.如果map没有指定的key,则返回null.
getOrDefault
hashCode
boolean isEmpty(); 如果map没有存储任何key-value,则返回true.
Set<K> keySet(); 返回map包含所有的key的一个set集合视图.
merge
V put(K key, V value); put方法是将指定的key-value存储到map里面的操作.如果map之前包含了一个此key对应的映射,那么此key对应的旧value值会被新的value值替换.
void putAll(Map<? extends K, ? extends V> m); putAll方法是将一个指定map的映射拷贝到当前map.
putIfAbsent
V remove(Object key); remove方法用于移除map中已有的某个key.本方法会返回移除的key对应的value值,如果map这个key没有对应的value值,则返回null.
remove
replace
replace
replaceAll
int size(); 返回map中key-value映射的个数.如果map包含的key-value个数超过了Integer.MAX_VALUE这个数, 则返回Integer.MAX_VALUE.
values


## 内部类
Entry