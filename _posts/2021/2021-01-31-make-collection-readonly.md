---
layout: post
title: 怎么做到集合真正只读？
category: Java
tags: [Collection, readonly]
keywords: Collection,readonly
excerpt: 本文从编译时和运行时的角度来考虑设置集合只读。
---

在有些场景中，需要设置集合只读。本文从`编译时`和`运行时`的角度来考虑设置集合只读。

## 环境

### 编译时

**使用 Use <? extends T>**

```
final List<? extends String> list = new ArrayList<>();
list.add("a"); // Compile failed according to PECS rule
```

但是以上代码并不能保证集合只读。比如以下示例代码。

```
final List<? extends String> list = new ArrayList<>();
System.out.println(list); // []
final List<String> list2 = (List<String>) list;
list2.add("a");
System.out.println(list); // [a]
```

### 编译时

**使用 Arrays.asList()**

```
final List<String> list = Arrays.asList();
list.add("a"); // java.lang.UnsupportedOperationException
```

**使用 Collections.singleton()/Collections.singletonList()/Collections.singletonMap()**

```
final Set<String> set = Collections.singleton("a");
set.add("b"); // java.lang.UnsupportedOperationException

final List<String> list = Collections.singletonList("a");
list.add("b"); // java.lang.UnsupportedOperationException

final Map<String, String> map = Collections.singletonMap("a", "1");
map.put("b", "2"); // java.lang.UnsupportedOperationException
```

**使用 ImmutableSet.of()/ImmutableList.of()/ImmutableMap.of()**

```
final Set<String> set = ImmutableSet.of("a");
set.add("b"); // java.lang.UnsupportedOperationException

final List<String> list = ImmutableList.of("a");
list.add("b"); // java.lang.UnsupportedOperationException

final Map<String, String> map = ImmutableMap.of("a", "1");
map.put("b", "2"); // java.lang.UnsupportedOperationException
```

**使用 Collections.unmodifiableSet()/Collections.unmodifiableList()/Collections.unmodifiableMap()**

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

## 总结

* 假如代码写得很规范，比如不用类型强制转换，使用 <? extends T> 就够了。

* 要完全保证集合只读，必须考虑运行时的设置。

* 要完美保证集合只读，编译时和运行时都必须同时考虑。
