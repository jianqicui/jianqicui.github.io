---
layout: post
title: Java 引用类型
category: Java
tags: [Java, 引用类型]
keywords: Java,引用类型
excerpt: 为了更灵活的控制对象的生命周期，引用被划分为强引用、软引用、弱引用、虚引用四种类型，每种类型有不同的生命周期，它们不同的地方就在于垃圾回收器对待它们会使用不同的处理方式。
---

为了更灵活的控制对象的生命周期，引用被划分为强引用、软引用、弱引用、虚引用四种类型，每种类型有不同的生命周期，它们不同的地方就在于垃圾回收器对待它们会使用不同的处理方式。

## 强引用

### 定义

> 强引用是使用最普遍的引用。如果一个对象具有强引用，那垃圾回收器宁愿抛出 OOM（OutOfMemoryError）也不会回收它。

例如：

```
Object o=new Object();   //  强引用
```

> 强可达<br/>
如果一个对象与 GC Roots 之间存在强引用，则称这个对象为强可达（strong reachable）对象。

### 特点

在 JVM 进行 GC 的时候，只要对象有强引用与其关联，就绝对不会对它进行回收，即使已经内存不足了也不会收回有强引用指向的对象。

如果你不需要使用某个对象了，可以将相应的引用设置为 null，消除强引用来帮助垃圾回收器进行回收。

如果在一个方法的内部有一个变量 s 持有一个对象（Object）的强引用，那么这个变量 s 保存在栈中，而真正的引用内容（object）保存在堆中。当这个方法运行完成后就会退出方法栈，则引用 s 也会被销毁，这个 object 就会被回收。但是当这个 s 是全局变量时，就需要在不再使用这个对象时赋值为 null，因为有强引用关联的对象是不会被垃圾回收的。

```
A a = new A();
B b = new B(a);
a = null;
```

这里 a 和 b 是强引用，当把 a = null 时，这时 a 不再指向 A 的地址。讲道理：当某个对象不再与其他引用关联时，就会被垃圾回收器判定为可回收，在 GC 中就会被回收掉。但是这里 a = null 时，A 对象不能被回收，因为还有一个 B 对象持有其强引用，这时候就造成了内存泄漏。

另一个内存泄露的例子：

```
public static ArrayList<Object> list = new ArrayList<Object>();

public void stackOverflowTest(Object object) {
    list.add(object);
    object = null;
}
```

### 小结

* 强引用就是最普通的引用

* 可以使用强引用直接访问目标对象

* 强引用指向的对象在任何时候都不会被系统回收

* 强引用可能会导致内存泄漏

* 过多的强引用会导致 OOM

## 软引用

### 定义

> 软引用是使用 SoftReference 创建的引用，强度弱于强引用，被其引用的对象在内存不足的时候会被回收，不会产生内存溢出。

```
String str=new String("abc");                                    // 强引用
SoftReference<String> softRef=new SoftReference<String>(str);    // 软引用
str = null;     // 消除强引用，现在只剩下软引用与其关联，该String对象为软可达状态
log.info("{}",softRef.get());
System.gc();
log.info("{}",softRef.get());

// 可以看到在内存充足的情况下，SoftReference 引用的对象是不会被回收的。
abc
abc
```

> 软可达<br/>
如果一个对象与 GC Roots 之间不存在强引用，但是存在软引用，则称这个对象为软可达（soft reachable）对象。

### 特点

如果一个对象与 GC Roots 之间存在软引用关联时，那么垃圾回收器对它的态度就取决于内存的紧张程度了。如果内存空间足够，垃圾回收器就不会回收这个对象，但如果内存空间不足了，它就难逃被回收的厄运。

在垃圾回收器没有回收它的时候，软可达对象就像强可达对象一样，可以被程序正常访问和使用，但是需要通过软引用对象间接访问，需要的话也能重新使用强引用将其关联。所以软引用适合用来做内存敏感的高速缓存。

在垃圾回收器回收一个对象前，SoftReference 类所提供的 get 方法会返回 Java 对象的强引用，一旦垃圾线程回收该对象之后，get 方法将返回 null。所以在获取软引用对象的代码中，一定要先判断返回是否为 null，以免出现 NullPointerException 异常而导致应用崩溃。

注意，SoftReference 对象是用来保存软引用的，但它同时也是一个 Java 对象。所以，当软可及对象被回收之后，虽然这个 SoftReference 对象的 get() 方法返回 null，但 SoftReference 对象本身并不是 null，而此时这个 SoftReference 对象已经不再具有存在的价值，需要一个适当的清除机制，避免大量 SoftReference 对象带来的内存泄漏。

ReferenceQueue 就是用来保存这些需要被清理的引用对象的。软引用可以和一个引用队列（ReferenceQueue）联合使用，如果软引用所引用的对象被垃圾回收器回收，Java 虚拟机就会把这个软引用加入到与之关联的引用队列中。

## 弱引用

### 定义

> 弱引用是使用 WeakReference 创建的引用，弱引用也是用来描述非必需对象的，它是比软引用更弱的引用类型。在发生 GC 时，只要发现弱引用，不管系统堆空间是否足够，都会将对象进行回收。

```
String str=new String("abc");    
WeakReference<String> weakRef = new WeakReference<String>(str);
str=null;
log.info("{}",weakRef.get());
System.gc();
log.info("{}",weakRef.get());

// 我们看到 gc 过后，弱引用的对象被回收掉了。
abc
null
```

> 弱可达<br/>
如果一个对象与 GC Roots 之间仅存在弱引用，则称这个对象为弱可达（weakly reachable）对象。

### 特点

如果这个对象是偶尔的使用，并且希望在使用时随时就能获取到，但又不想影响此对象的垃圾收集，那么你应该用 Weak Reference 来记住此对象。

弱引用可以和一个引用队列（ReferenceQueue）联合使用，如果弱引用所引用的对象被垃圾回收，Java 虚拟机就会把这个弱引用加入到与之关联的引用队列中。

一般来说，很少直接使用 WeakReference，而是使用 WeakHashMap。在 WeakHashMap 中，内部有一个引用队列，插入的元素会被包裹成 WeakReference，并加入队列中，用来做缓存再合适不过。

在 Tomcat 的缓存中，其实就用到了 WeakHashMap：

```
public final class ConcurrentCache {
    private final int size;

    private final Map<K, V> eden;
    private final Map<K, V> longterm;

    public ConcurrentCache(int size) {
        this.size = size;
        this.eden = new ConcurrentHashMap<>(size);
        this.longterm = new WeakHashMap<>(size);
    }

    public V get(K k) {
        // 先从eden中取
        V v = this.eden.get(k);
        if (v == null) {
            // 如果取不到再从longterm中取
            synchronized (longterm) {
                v = this.longterm.get(k);
            }
            // 如果取到则重新放到eden中
            if (v != null) {
                this.eden.put(k, v);
            }
        }
        return v;
    }

    public void put(K k, V v) {
        if (this.eden.size() >= size) {
            // 如果eden中的元素数量大于指定容量，将所有元素放到longterm中
            synchronized (longterm) {
                this.longterm.putAll(this.eden);
            }
            this.eden.clear();
        }
        this.eden.put(k, v);
    }
}
```

经过这样的设计，相对常用的对象都能在 eden 缓存中找到，不常用（有可能被销毁的对象）的则进入 longterm 缓存。而 longterm 的 key 的实际对象没有其他引用指向它时，gc 就会自动回收 heap 中该弱引用指向的实际对象，并将弱引用放入其引用队列中。

## 弱引用与软引用对比

弱引用与软引用的区别在于：

* 只具有弱引用的对象拥有更短暂的生命周期。

* 被垃圾回收器回收的时机不一样，在垃圾回收器线程扫描它所管辖的内存区域的过程中，一旦发现了只具有弱引用的对象，不管当前内存空间足够与否，都会回收它的内存。而被软引用关联的对象只有在内存不足时才会被回收。

* 弱引用不会影响 GC，而软引用会一定程度上对 GC 造成影响。

相似之处：都是用来描述非必需对象的。

那么什么时候用 SoftReference，什么时候用 WeakReference 呢？

如果缓存的对象是比较大的对象，使用频率相对较高的对象，那么使用 SoftReference 会更好，因为这样能让缓存对象有更长的生命周期。

如果缓存对象都是比较小的对象，使用频率一般或者相对较低，那么使用 WeakReference 会更合适。

当然，如果实在不知道选哪个，一般而言，用作缓存时使用 WeakHashMap 都不会有太大问题。

## 虚引用

### 定义

> 虚引用是使用 PhantomReference 创建的引用，虚引用也称为幽灵引用或者幻影引用，是所有引用类型中最弱的一个。一个对象是否有虚引用的存在，完全不会对其生命周期构成影响，也无法通过虚引用获得一个对象实例。<br/>
虚可达<br/>
如果一个对象与 GC Roots 之间仅存在虚引用，则称这个对象为虚可达（phantom reachable）对象。

### 特点

虚引用作用在于跟踪垃圾回收过程，在对象被收集器回收时收到一个系统通知。当垃圾回收器准备回收一个对象时，如果发现它还有虚引用，就会在垃圾回收后，将这个虚引用加入引用队列，在其关联的虚引用出队前，不会彻底销毁该对象。所以可以通过检查引用队列中是否有相应的虚引用来判断对象是否已经被回收了。

如果一个对象没有强引用和软引用，对于垃圾回收器而言便是可以被清除的，在清除之前，会调用其 finalize 方法，如果一个对象已经被调用过 finalize 方法但是还没有被释放，它就变成了一个虚可达对象。

使用虚引用的目的就是为了得知对象被 GC 的时机，所以可以利用虚引用来进行销毁前的一些操作，比如说资源释放等。这个虚引用对于对象而言完全是无感知的，有没有完全一样，但是对于虚引用的使用者而言，就像是待观察的对象的把脉线，可以通过它来观察对象是否已经被回收，从而进行相应的处理。

事实上，虚引用有一个很重要的用途就是用来做堆外内存的释放，DirectByteBuffer 就是通过虚引用来实现堆外内存的释放的。

### 小结

* 虚引用是最弱的引用

* 虚引用对对象而言是无感知的，对象有虚引用跟没有是完全一样的

* 虚引用不会影响对象的生命周期

* 虚引用可以用来做为对象是否存活的监控

## 总结

引用级别：强引用 > 软引用 > 弱引用 > 虚引用

| 引用类型 | 引用对象被垃圾回收的时间 | 用途 | 对应的类 |
| ---- | ---- | ---- | ---- |
| 强引用 | 从来不会 | 一般用途，保持对象不被回收 | 默认 |
| 软引用 | 内存不足时 | 缓存，对象在内存足够时不被回收 | SoftReference |
| 弱引用 | 垃圾回收时 | 缓存，对象仅在使用时不被回收 | WeakReference<br/>WeakHashMap |
| 虚引用 | 进行垃圾回收时 | 跟踪 GC 过程，在对象被回收前进行一些清理工作 | PhantomReference |

## 参考

[https://segmentfault.com/a/1190000021187017](https://segmentfault.com/a/1190000021187017)

[https://juejin.im/post/6844904147158843400](https://juejin.im/post/6844904147158843400)
