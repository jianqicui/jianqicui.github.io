---
layout: post
title: What can LinkedList/LinkedHashSet/LinkedHashMap do?
category: Java
tags: [LinkedList, LinkedHashSet, LinkedHashMap]
keywords: LinkedList,LinkedHashSet,LinkedHashMap
excerpt: What can LinkedList/LinkedHashSet/LinkedHashMap do?
---

## Question

`LinkedList`/`LinkedHashSet`/`LinkedHashMap`, these three do not have `ArrayList`/`HashSet`/`HashMap` popular, what can they do?

## List

### ArrayList

An `ordered` list based on an array. `Insertion order`, that is, `getting order`.

### LinkedList

An `ordered` list based on a linked list. `Insertion order`, that is, `getting order`.

## Set

### HashSet

A collection with `unique` elements.

### TreeSet

A collection with `unique` and `sorted` elements. The `default sorter` or `customized sorter` can be used.

### LinkedHashSet

A collection with `unique` and `ordered` elements. `Insertion order`, that is, `getting order`.

## Map

### HashMap

Mapping table.

### TreeMap

A `sorted` mapping table. The `default sorter` or `customized sorter` can be used.

### LinkedHashMap

A `ordered` mapping table. `Insertion order`, that is, `getting order`.

## Code

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
