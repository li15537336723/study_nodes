# Set



## 1 Set 接口基本介绍



1）无序（添加和取出的顺序不一致），没有索引

2）不许重复元素，所以最多包含一个 null



```
/**
    1. 以 Set 接口的实现类 HashSet 来讲解 Set 接口的方法
    2. set 接口的实现类的对象（Set接口对象），不能存放重复的元素，可以添加一个 null
    3. set 接口对象存放数据是无序（即添加的顺序和取出的顺序不一致）
    4. 注意：取出的顺序虽然不是添加的顺序，但是取出的顺序是固定的
*/
```



## 2 Hash 全面说明

1）HashSet 实现了Set接口

2）HashSet 实际上是 HashMap

<img src="C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210807170135572.png" alt="image-20210807170135572" style="zoom:67%;" />

3）可以存放 null 值，但是只能有一个 null

4）HashSet 不保证元素是有序的，取决于 hash 后，再确定索引的结果

5）不能有重复元素 / 对象



## 3 HashSet 底层机制说明

分析 HashSet 的添加元素底层是如何实现的



1. HashSet 底层是 HashMap
2. 添加一个元素时，先得到 hash 值，会转成-> 索引值
3. 找到存储数据表 table，看到这个索引位置是否已经存放的有元素
4. 如果没有，直接加入
5. 如果有，调用 equals 比较，如果相同，就放弃添加，如果不相同，则添加到最后
6. 在 Java8 中，如果一条链表的元素个数超过 TREEIFY_THRESHOLD (默认是 8)，并且 table 的大小 >= MIN_TREEIFY_CAPACITY（默认64）就会进行树化（红黑树）



































































































