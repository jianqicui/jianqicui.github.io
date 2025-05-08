---
layout: post
title: 什么是FutureTask？
category: Java
tags: [Java, FutureTask]
keywords: Java,FutureTask
excerpt: 什么是FutureTask？
---

什么是FutureTask？

在 Java 并发程序中 `FutureTask` 表示一个可以取消的异步运算。它有启动和取消运算、查询运算是否完成和取回运算结果等方法。只有当运算完成的时候结果才能取回，如果运算尚未完成 get 方法将会阻塞。一个 `FutureTask` 对象可以对调用了 Callable 和 Runnable 的对象进行包装，由于 `FutureTask` 也是调用了 Runnable 接口所以它可以提交给 Executor 来执行。
