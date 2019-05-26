---
title: JAVASE学习笔记—007 Java常用类库—List、Set、Map集合接口及实现类
date: 2019-05-26 11:16:34
tags:
- java
- javase
category:
- java
---

## 一、List接口和实现类

List接口继承自—Collection—Iterable。
Iterable接口以循环迭代方式访问集合中的数据元素，定义了唯一一个返回指向集合的Iterable迭代器。它主要通过foreach方式处理集合中的元素。它的任何实现类都可以通过获取指向集合的迭代实现对集合的遍历。
List接口间接继承Iterable接口，它的直接父接口Collection定义了更多操作方法。

### 常用方法
1. booleadn add(Object o)                   在List末尾添加元素
2. void add(int index, Object element)      在指定索引位置添加元素
2. void clear()                             清空List
3. boolean contains(Object o)               是否包含对象o
4. Object get(int index)                    获取指定索引的元素
5. boolean isEmpty()                        List是否为空
6. Iterator<E> iterator()                   获取迭代器，Iterable接口的方法
7. Object remove(int index)                 移除指定索引的元素
8. boolean remove(Object o)                 移除指定对象
9. Object set(int index, Object element)    设置指定索引的元素，返回旧元素
10. int indexOf(String target, int fromIndex)
11. int size()                              返回List的长度

增删改查：
1. 增
    1. add
2. 查
    1. 获取元素 get
    2. 包含元素 contains
    3. 遍历集合 for、iterator
    4. 集合长度 size
    5. 获取字符串元素索引 indexOf
4. 改
    1. 新元素代替旧元素 set
5. 删
    1. 删除元素 remove
    2. 清空集合 clear
    3. 是否为空 isEmpty()

### List接口实现类——ArrayList

ArrayList类是List接口长度可变数组的实现。
ArrayList是非线程安全的。
ArrayList查找访问集合元素速度比较快，删除、插入随机元素操作比较慢。

### List接口实现类——Vector

Vector是向量集合，是List接口长度可变数组的实现。
Vector是线程安全的。
Vector相比ArrayList效率要低，多用于被多个线程同时访问的数据处理。

**Vector除了List的方法，还实现返回Enumeration枚举接口的方法elements()。**

## 二、Set接口和实现类

### Set接口规范

Set接口继承自—Collection—Iterable。
Set接口只能存储不相同的对象。Set接口是数学Set的抽象描述。
和List不同，Set中的元素不带任何索引，不能使用索引访问元素。
Set接口添加，遍历比较慢，随机删除修改数据速度较List快。

### 常用方法

1. Object[] toArray()

### Set实现类——HashSet

HashSet由哈希表支持，不保证set的迭代顺序，允许使用null元素。
HashSet是非线程安全的。

### Set实现类——TreeSet

TreeSet实现了NavigableSet接口，NavigableSet继承了SortedSet接口。
TreeSet底层是由TreeMap实现的。
TreeSet可以在创建时，用自定义的Comparator进行排序。
TreeSet不是线程安全的。

#### 常用方法

1. public TreeSet(Comparator<E> comparator) 指定自定义排序
2. public Iterator<E> descendingIterator()  返回倒序迭代器    
3. public E last()  获取最后一个元素
4. public E first() 获取第一个元素

#### TreeSet自定义排序

1. 自定义java.util.Comparator接口的实现类，实现compare方法
2. 调用TreeSet的Comparator类型构造器创建对象

### Set实现类——LinkedHashSet

基于哈希表和链表的Set，是有序的，元素按照添加的顺序排序。
LinkedHashList由于需要维护元素顺序，因此效率比HashSet低。 

## 实践

### 遍历List时候修改List长度，抛出异常

``` java
for (Object o : list1){
    if (list2.contains(data)){
        list1.remove(o);    //改变List长度
    }else {
        list3.add(o);
}
/*
Exception in thread "main" java.util.ConcurrentModificationException
	at java.util.ArrayList$Itr.checkForComodification(ArrayList.java:909)
	at java.util.ArrayList$Itr.next(ArrayList.java:859)
	at work.collection.exercise04.main(exercise04.java:131)
*/
```

调用堆栈：
1. ArrayList$Itr.next()调用checkForComodification()方法
2. checkForComodification()方法，if(modCount != expectedModCount)，抛出 ConcurrentModificationException
3. ArrayList类的boolean remove(Object o)方法，调用fastRemove(int index)方法
fastRemove()方法中，modCount++;

因此，modCount != expectedModCount，抛出异常

解析：
ArrayList线程不安全，List保证其遍历时不被修改，采用的是用一个计数器的机制。
在开始遍历前，先记录当前的modCount值，而后每次访问下一个元素之前，都会检查下modCount值是否变化，如果有变化，说明List的长度有变化。
一旦长度有变化，就会抛出ConcurrentModificationException异常。