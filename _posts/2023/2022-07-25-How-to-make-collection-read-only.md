---
layout: post
title: How to make collection read only?
category: Java
tags: [collection, read only]
keywords: collection,read only
excerpt: How to make collection read only?
---

## Background

In most requirements, collection needs to be made as read only.
There are some ways considered from `compile time` and `run time`.

## Environments

### Compile time

#### Use <? extends T>

```
final List<? extends String> list = new ArrayList<>();
list.add("a"); // Compile failed according to PECS rule
```

But, above usage can not guarantee the read only of collection.

```
final List<? extends String> list = new ArrayList<>();
System.out.println(list); // []
final List<String> list2 = (List<String>) list;
list2.add("a");
System.out.println(list); // [a]
```

### Run time

#### Use Arrays.asList()

```
final List<String> list = Arrays.asList();
list.add("a"); // java.lang.UnsupportedOperationException
```

#### Use Collections.singleton()/Collections.singletonList()/Collections.singletonMap()

```
final Set<String> set = Collections.singleton("a");
set.add("b"); // java.lang.UnsupportedOperationException

final List<String> list = Collections.singletonList("a");
list.add("b"); // java.lang.UnsupportedOperationException

final Map<String, String> map = Collections.singletonMap("a", "1");
map.put("b", "2"); // java.lang.UnsupportedOperationException
```

#### Use ImmutableSet.of()/ImmutableList.of()/ImmutableMap.of()

```
final Set<String> set = ImmutableSet.of("a");
set.add("b"); // java.lang.UnsupportedOperationException

final List<String> list = ImmutableList.of("a");
list.add("b"); // java.lang.UnsupportedOperationException

final Map<String, String> map = ImmutableMap.of("a", "1");
map.put("b", "2"); // java.lang.UnsupportedOperationException
```

#### Use Collections.unmodifiableSet()/Collections.unmodifiableList()/Collections.unmodifiableMap()

```
Set<String> set = new HashSet<>();
set.add("a");
set = Collections.unmodifiableSet(set);
set.add("b"); // java.lang.UnsupportedOperationException

List<String> list = new ArrayList<>();
list.add("a");
list = Collections.unmodifiableList(list);
list.add("b"); // java.lang.UnsupportedOperationException

Map<String, String> map = new HashMap<>();
map.put("a", "1");
map = Collections.unmodifiableMap(map);
map.put("b", "2"); // java.lang.UnsupportedOperationException
```

## Conclusion

* If programming rule is followed in coding, using <? extends T> in compile time is enough. E.g, no class casting is used.
* To guarantee the read only of collection, the ways in run time should be considered.
The perfect way to make collection read only is considering compile time and run time both.
