Set

# 基本概念
线程不安全，根据实现方式不同，有的有序，有的无序。元素不重复。

# 常用实现类，接口，抽象类
## 线程不安全
+ EnumSet有序不重复
+ TreeSet有序不重复
+ HashSet无序不重复
+ LinkedHashSet有序不重复？
+ NavigableSet有序不重复

## 线程安全
+ ConcurrentSkipListSet 实现了NavigableSet接口和继承自AbstractSet的Set集合类，它是线程安全的，内部存储实际是存在ConcurrentNavigableMap中。
+ CopyOnWriteArraySet 底层实现是CopyOnWriteArrayList，线程安全的。