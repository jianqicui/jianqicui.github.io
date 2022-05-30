---
layout: post
title: putIfAbsent 和 computeIfAbsent 使用说明及示例代码
category: Java
tags: [Map, putIfAbsent, computeIfAbsent]
keywords: Map,putIfAbsent,computeIfAbsent
excerpt: 本文主要介绍 Java 中 Map 的 putIfAbsent 和 computeIfAbsent 使用的方法和示例代码。
---

本文主要介绍 Java 中 Map 的 putIfAbsent 和 computeIfAbsent 使用的方法和示例代码。

## putIfAbsent 使用

之前的 put 方法，只要 key 存在，value 值就会被覆盖，注意 put 方法返回的是 put 之前的值，如果无 put 之前的值，则返回 null。putIfAbsent 方法，只有在 key 不存在或者 key 对应 value 为 null 的时候，value 值才会被覆盖。

**putIfAbsent 源代码如下：**

```
default V putIfAbsent(K key, V value) {
    V v = get(key);
    if (v == null) {
        v = put(key, value);
    }
    return v;
}
```

**使用 put() 方法实现相同效果，如下代码：**

```
if (map.containsKey("kk") && map.get("kk") != null) {
    map.put("kk", "vv");
}
```

**使用场景：**如果我们要变更某个 key 的值，我们又不知道 key 是否存在的情况下，而又不需要新增 key 的情况使用。

## computeIfAbsent 使用

​简单来说，如果 map 里没有这个 key，那么就按照后面的这个 function 添加对应的 key 和 value。如果要是有这个 key，那么就不添加。

**computeIfAbsent 源代码如下：**

```
default V computeIfAbsent(K key,
           Function<? super K, ? extends V> mappingFunction) { // 第一个参数为 Map 的 key，第二个参数是一个函数接口
    Objects.requireNonNull(mappingFunction);
    V v;
    // 将第一个参数作为 key，获取 value，并将其赋给 v ，判断是否为 null
    if ((v = get(key)) == null) { // 若 v 为 null，则创建一个新的值 newValue
        V newValue;
        if ((newValue = mappingFunction.apply(key)) != null) {
            put(key, newValue);
            return newValue;
        }
    }
    return v;
}
```

**使用示例代码：**

```
Map<String, Integer> map = new HashMap<>();
map.put("Pen", 10);
map.put("Book", 500);
map.put("Clothes", 400);
map.put("Mobile", 5000);

System.out.println("hashMap: " + map.toString());

// 为缺少的新 key 提供值
// 使用 computeIfAbsent 方法
map.computeIfAbsent("newPen", k -> 600);
map.computeIfAbsent("newBook", k -> 800);

System.out.println("new hashMap: " + map);
```

**输出结果：**

```
hashMap：{Book = 500，Mobile = 5000，Pen = 10，Clothes = 400}
new hashMap：{newPen = 600，Book = 500，newBook = 800，Mobile = 5000，Pen = 10，Clothes = 400}
```

**使用场景：**我们不知道 key 是否存在，只是新增 key 及对应的值，不对之前的 key 和值做修改。

## 参考

[https://www.cjavapy.com/article/239/](https://www.cjavapy.com/article/239/)

[http://www.javashuo.com/article/p-vwgwabyb-oa.html](http://www.javashuo.com/article/p-vwgwabyb-oa.html)
