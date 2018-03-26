# 基本概念
Stack继承自Vector。堆栈类，后进先出（LIFO）。

Stack内部一共五个方法，其中三个方法都加了synchronized关键字，是线程安全的。
而push和empty方法调用了Vector的方法，由于Vector中的addElement()和size()方法都是线程安全的，
所以Stack的每个方法也都是线程安全的，所以它和Vector一样，都是线程安全的。

jdk1.8现在已不建议使用。

# 主要方法
+ push 向堆栈顶压入一个元素。
```java
public E push(E item) {
        addElement(item);

        return item;
    }
```
