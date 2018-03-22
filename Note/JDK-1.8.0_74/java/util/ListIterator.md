
ListIterator迭代器

# 基本概念
List接口所使用的迭代器，根据List的数据结构（数组，双向链表），继承Iterator接口，并在Iterator基础上增加了一系列方法声明。主要有增加，修改，索引相关方法。

# 主要提供方法
+ add(E e): 将指定的元素插入列表，插入位置为迭代器当前位置之前。
+ hasNext()：以正向遍历列表时，如果列表迭代器后面还有元素，则返回 true，否则返回false。
+ hasPrevious():如果以逆向遍历列表，列表迭代器前面还有元素，则返回 true，否则返回false。
+ next()：返回列表中ListIterator指向位置后面的元素。
+ nextIndex():返回列表中ListIterator所需位置后面元素的索引。
+ previous():返回列表中ListIterator指向位置前面的元素。
+ previousIndex()：返回列表中ListIterator所需位置前面元素的索引。
+ remove():从列表中删除next()或previous()返回的最后一个元素（有点拗口，意思就是对迭代器使用hasNext()方法时，删除ListIterator指向位置后面的元素；当对迭代器使用hasPrevious()方法时，删除ListIterator指向位置前面的元素）。
+ set(E e)：从列表中将next()或previous()返回的最后一个元素返回的最后一个元素更改为指定元素e。

# 与Iterator比较
## 相同点
+ 都是迭代器，当需要对集合中元素进行遍历不需要干涉其遍历过程时，这两种迭代器都可以使用。

## 不同点
+ 使用范围不同，Iterator可以应用于所有的集合，Set、List和Map和这些集合的子类型。而ListIterator只能用于List及其子类型。
+ ListIterator有add方法，可以向List中添加对象，而Iterator不能。
+ ListIterator和Iterator都有hasNext()和next()方法，可以实现顺序向后遍历，但是ListIterator有hasPrevious()和previous()方法，可以实现逆向（顺序向前）遍历。Iterator不可以。
+ ListIterator可以定位当前索引的位置，nextIndex()和previousIndex()可以实现。Iterator没有此功能。
+ 都可实现删除操作，但是ListIterator可以实现对象的修改，set()方法可以实现。Iterator仅能遍历，不能修改。
