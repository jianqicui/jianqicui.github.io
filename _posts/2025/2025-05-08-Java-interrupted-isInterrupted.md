---
layout: post
title: Java 中 interrupted 和 isInterrupted 方法的区别？
category: Java
tags: [Java, interrupted, isInterrupted]
keywords: Java,interrupted,isInterrupted
excerpt: Java 中 interrupted 和 isInterrupted 方法的区别？
---

Java 中 `interrupted` 和 `isInterrupted` 方法的区别？

`interrupted()` 和 `isInterrupted()` 的主要区别是前者会将中断状态清除而后者不会。Java多线程的中断机制是用内部标识来实现的，调用 Thread.interrupt() 来中断一个线程就会设置中断标识为 true。当中断线程调用静态方  Thread.interrupted() 来检查中断状态时，中断状态会被清零。而非静态方法 isInterrupted() 用来查询其它线程的中断状态且不会改变中断状态标识。简单的说就是任何抛出 InterruptedException 异常的方法都会将中断状态清零。无论如何，一个线程的中断状态有有可能被其它线程调用中断来改变。
