集合的迭代器。

# 基本概念
迭代器接口，定义了迭代器的基本方法，具体实现在各个集合的实现类中。

# 提供的主要方法
+ boolean hasNext(); 如果存在下一个元素，则返回真。迭代器没有移动。
+ E next(); 返回下一个元素。
+ void remove(); 
```java
/**
 * 从底层集合中删除该迭代器返回的最后一个元素（可选操作）。
 * 如果在迭代过程中，除了调用此方法之外，任何其他方法都在修改基础集合，则迭代器的行为是不确定的。
 */
default void remove() {
        throw new UnsupportedOperationException("remove");
    }
```
+ void forEachRemaining(Consumer<? super E> action); 
```java
/**
 * 对每个剩余元素执行给定的操作，直到所有元素都被处理或动作抛出异常为止。
 */
default void forEachRemaining(Consumer<? super E> action) {
        Objects.requireNonNull(action);
        while (hasNext())
            action.accept(next());
    }
```

