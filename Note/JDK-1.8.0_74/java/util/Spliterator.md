Spliterator（splitable iterator可分割迭代器）接口是Java为了并行遍历数据源中的元素而设计的迭代器。
这个可以类比最早Java提供的顺序遍历迭代器Iterator，但一个是顺序遍历，一个是并行遍历。

# 设计思想
总得来说就是用递归的方式把并行的任务拆分成更小的子任务，然后把每个子任务的结果合并起来生成整体结果。