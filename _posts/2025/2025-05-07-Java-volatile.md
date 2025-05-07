---
layout: post
title: Java volatile
category: Java
tags: [Java, volatile]
keywords: Java,volatile
excerpt: Java volatile
---

一种实践是用 `volatile` 修饰 long 和 double 变量，使其能按原子类型来读写。double 和 long 都是64位宽，因此对这两种类型的读是分为两部分的，第一次读取第一个 32 位，然后再读剩下的 32 位，这个过程不是原子的，但 Java 中 `volatile` 型的 long 或 double 变量的读写是原子的。

`volatile` 修复符的另一个作用是提供内存屏障（memory barrier），例如在分布式框架中的应用。简单的说，就是当你写一个 `volatile` 变量之前，Java 内存模型会插入一个写屏障（write barrier），读一个 `volatile` 变量之前，会插入一个读屏障（read barrier）。意思就是说，在你写一个 `volatile` 域时，能保证任何线程都能看到你写的值，同时，在写之前，也能保证任何数值的更新对所有线程是可见的，因为内存屏障会将其他所有写的值更新到缓存。
