---
layout: post
title: Java 中 synchronized 和 ReentrantLock 有什么不同？
category: Java
tags: [Java, synchronized, ReentrantLock]
keywords: Java,synchronized,ReentrantLock
excerpt: Java 中 synchronized 和 ReentrantLock 有什么不同？
---

Java 中 `synchronized` 和 `ReentrantLock` 有什么不同？

Java 在过去很长一段时间只能通过 `synchronized` 关键字来实现互斥，它有一些缺点。比如你不能扩展锁之外的方法或者块边界，尝试获取锁时不能中途取消等。Java 5 通过 Lock 接口提供了更复杂的控制来解决这些问题。`ReentrantLock` 类实现了 Lock，它拥有与 `synchronized` 相同的并发性和内存语义且它还具有可扩展性。