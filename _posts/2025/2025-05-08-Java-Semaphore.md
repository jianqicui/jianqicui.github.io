---
layout: post
title: Java 中 Semaphore 是什么？
category: Java
tags: [Java, Semaphore]
keywords: Java,Semaphore
excerpt: Java 中 Semaphore 是什么？
---

Java 中 `Semaphore` 是什么？

Java 中 `的Semaphore` 是一种新的同步类，它是一个计数信号。从概念上讲，信号量维护了一个许可集合。如有必要，在许可可用前会阻塞每一个 acquire()，然后再获取该许可。每个 release() 添加一个许可，从而可能释放一个正在阻塞的获取者。但是，不使用实际的许可对象，`Semaphore` 只对可用许可的号码进行计数，并采取相应的行动。