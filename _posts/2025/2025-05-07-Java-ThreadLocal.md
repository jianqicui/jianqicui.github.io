---
layout: post
title: Java ThreadLocal
category: Java
tags: [Java， ThreadLocal]
keywords: Java，ThreadLocal
excerpt: Java ThreadLocal
---

当使用 `ThreadLocal` 维护变量时，`ThreadLocal` 为每个使用该变量的线程提供独立的变量副本，每个线程都可以独立地改变自己的副本，而不会影响其它线程所对应的副本，是线程隔离的。线程隔离的秘密在于 `ThreadLocalMap` 类。

线程局部变量是局限于线程内部的变量，属于线程自身所有，不在多个线程间共享。Java 提供 ThreadLocal 类来支持线程局部变量，是一种实现线程安全的方式。但是在管理环境下（如 web 服务器）使用线程局部变量的时候要特别小心，在这种情况下，工作线程的生命周期比任何应用变量的生命周期都要长。任何线程局部变量一旦在工作完成后没有释放，Java 应用就存在内存泄露的风险。

ThreadLocal的方法：void set(T value)、T get() 以及 T initialValue()。

`ThreadLocal` 是如何为每个线程创建变量的副本的：

首先，在每个线程 Thread 内部有一个 ThreadLocal.ThreadLocalMap 类型的成员变量 threadLocals，这个 threadLocals 就是用来存储实际的变量副本的，键值为当前 `ThreadLocal` 变量，value 为变量副本（即 T 类型的变量）。初始时，在 Thread 里面，threadLocals 为空，当通过 ThreadLocal 变量调用 get() 方法或者 set() 方法，就会对 Thread 类中的 threadLocals 进行初始化，并且以当前 `ThreadLocal` 变量为键值，以 ThreadLocal 要保存的副本变量为value，存到 threadLocals。然后在当前线程里面，如果要使用副本变量，就可以通过 get 方法在 threadLocals 里面查找。

总结：
* 实际的通过 ThreadLocal 创建的副本是存储在每个线程自己的 threadLocals 中的
* 为何 threadLocals 的类型 ThreadLocalMap 的键值为 ThreadLocal 对象，因为每个线程中可有多个 threadLocal 变量，就像上面代码中的 longLocal 和 stringLocal
* 在进行 get 之前，必须先 set，否则会报空指针异常；如果想在 get 之前不需要调用 set 就能正常访问的话，必须重写 initialValue() 方法
