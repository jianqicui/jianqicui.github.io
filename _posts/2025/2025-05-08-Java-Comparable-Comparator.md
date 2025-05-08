---
layout: post
title: Comparable 与 Comparator 有什么不同？
category: Java
tags: [Java, Comparable, Comparator]
keywords: Java,Comparable,Comparator
excerpt: Comparable 与 Comparator 有什么不同？
---

Java 中，`Comparable` 与 `Comparator` 有什么不同？

`Comparable` 接口用于定义对象的自然顺序，而 `Comparator` 通常用于定义用户定制的顺序。
`Comparable` 总是只有一个，但是可以有多个 `Comparator` 来定义对象的顺序。
