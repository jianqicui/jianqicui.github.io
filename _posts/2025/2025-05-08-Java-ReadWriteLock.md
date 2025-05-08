---
layout: post
title: Java 中的 ReadWriteLock 是什么？
category: Java
tags: [Java, ReadWriteLock]
keywords: Java,ReadWriteLock
excerpt: Java 中的 ReadWriteLock 是什么？
---

Java 中的 `ReadWriteLock` 是什么？

一般而言，读写锁是用来提升并发程序性能的锁分离技术的成果。Java中的 `ReadWriteLock`是 Java 5 中新增的一个接口，一个 `ReadWriteLock` 维护一对关联的锁，一个用于只读操作一个用于写。在没有写线程的情况下一个读锁可能会同时被多个读线程持有。写锁是独占的，你可以使用 JDK 中的 ReentrantReadWriteLock 来实现这个规则，它最多支持 65535 个写锁和 65535 个读锁。
