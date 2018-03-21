实现此接口允许对象成为“for-each loop”语句的目标。提供了迭代器。

# 内部成员变量
Iterator<T> iterator(); 返回元素类型对应的迭代器。需要子类来实现一个内部迭代器Iterator遍历元素

# 主要提供的方法
+ forEach
```java
/**
 * 对这个Iterable的每一个元素，执行给定的动作，直到所有元素都被处理或者动作抛出一个异常为止。
 */
default void forEach(Consumer<? super T> action) {
        Objects.requireNonNull(action);
        for (T t : this) {
            action.accept(t);
        }
    }
```

+ Spliterator
```java
/**
 * 创建并返回一个可分割迭代器。默认实现应该总是被重写。
 */
default Spliterator<T> spliterator() {
        return Spliterators.spliteratorUnknownSize(iterator(), 0);
    }
```
