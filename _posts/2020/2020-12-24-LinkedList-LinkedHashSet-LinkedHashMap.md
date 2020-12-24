---
layout: post
title: LinkedList/LinkedHashSet/LinkedHashMap 能干啥？
category: Java
tags: [LinkedList, LinkedHashSet, LinkedHashMap]
keywords: LinkedList,LinkedHashSet,LinkedHashMap
excerpt: LinkedList/LinkedHashSet/LinkedHashMap，这三个没有 ArrayList/HashSet/HashMap 大众化，都能干点啥？
---

LinkedList/LinkedHashSet/LinkedHashMap，这三个没有 ArrayList/HashSet/HashMap 大众化，都能干点啥？

## List

### ArrayList

基于数组的有序列表。插入顺序，即取出顺序。

### LinkedList

基于链表的有序列表。插入顺序，即取出顺序。

## Set

### HashSet

元素唯一的集合。

### TreeSet

元素唯一且排序的集合。可使用默认排序器，也可自定义排序器。

### LinkedHashSet

元素唯一且有序的集合。插入顺序，即取出顺序。

## Map

### HashMap

映射表。

### TreeMap

排序的映射表。可使用默认排序器，也可自定义排序器。

### LinkedHashMap

有序的映射表。插入顺序，即取出顺序。

## 代码示例

```
@Test
public void testLinkedHashSet1() {
    // Which set can keep the order of elements in array1?

    final String[] array1 = { "c", "b", "a" };

    final Set<String> hashSet = new HashSet<>();
    final Set<String> linkedHashSet = new LinkedHashSet<>();

    for (final String s : array1) {
        hashSet.add(s);
        linkedHashSet.add(s);
    }

    System.out.println(hashSet); // [a, b, c]
    System.out.println(linkedHashSet); // [c, b, a]
}

@Test
public void testLinkedHashSet2() {
    // Keep the order of the elements in array2 and make them distinct

    final String[] array2 = { "a", "a", "b", "b", "c", "c" };

    final Collection<String> arrayList = new ArrayList<String>();
    final Collection<String> linkedHashSet = new LinkedHashSet<String>();

    for (final String s : array2) {
        // Bad smell, without LinkedHashSet
        if (!arrayList.contains(s)) {
            arrayList.add(s);
        }

        // Good smell, with LinkedHashSet
        linkedHashSet.add(s);
    }

    System.out.println(arrayList); // [a, b, c]
    System.out.println(linkedHashSet); // [a, b, c]
}
```
