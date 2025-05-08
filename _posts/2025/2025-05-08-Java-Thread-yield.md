---
layout: post
title: Thread 类中的 yield 方法有什么作用？
category: Java
tags: [Java, Thread, yield]
keywords: Java,Thread,yield
excerpt: Thread 类中的 yield 方法有什么作用？
---

`Thread` 类中的 `yield` 方法有什么作用？

`yield()` 方法可以暂停当前正在执行的线程对象，让其它有相同优先级的线程执行。它是一个静态方法而且只保证当前线程放弃 CPU 占用而不能保证使其它线程一定能占用 CPU，执行 `yield()` 的线程有可能在进入到暂停状态后马上又被执行。