---
layout: post
title: Java 中 Runnable 和 Callable 有什么不同？
category: Java
tags: [Java, Runnable, Callable]
keywords: Java,Runnable,Callable
excerpt: Java 中 Runnable 和 Callable 有什么不同？
---

Java 中 `Runnable` 和 `Callable` 有什么不同？

`Runnable` 和 `Callable` 都代表那些要在不同的线程中执行的任务。`Runnable` 从 JDK 1.0 开始就有了，`Callable` 是在 JDK 1.5 增加的。它们的主要区别是 `Callable` 的 call() 方法可以返回值和抛出异常，而 `Runnable` 的 run() 方法没有这些功能。`Callable` 可以返回装载有计算结果的 `Future` 对象。
