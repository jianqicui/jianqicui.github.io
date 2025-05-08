---
layout: post
title: volatile 变量和 atomic 变量有什么不同？
category: Java
tags: [Java, volatile, atomic]
keywords: Java,volatile,atomic
excerpt: volatile 变量和 atomic 变量有什么不同？
---

`volatile` 变量和 `atomic` 变量有什么不同？

这是个有趣的问题。首先，`volatile` 变量和 `atomic` 变量看起来很像，但功能却不一样。`volatile` 变量可以确保先行关系，即写操作会发生在后续的读操作之前, 但它并不能保证原子性。例如用 `volatile` 修饰 count 变量那么 count++ 操作就不是原子性的。而 AtomicInteger 类提供的 `atomic` 方法可以让这种操作具有原子性如 getAndIncrement() 方法会原子性的进行增量操作把当前值加一，其它数据类型和引用变量也可以进行相似操作。
