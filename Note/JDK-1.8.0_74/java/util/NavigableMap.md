# 基本概念
NavigableMap继承SortedMap，含有返回特定条件最近匹配的导航方法。

提供了大量的（key）范围比较筛选的方法。

```java
public interface NavigableMap<K,V> extends SortedMap<K,V> {
    //...
}
```