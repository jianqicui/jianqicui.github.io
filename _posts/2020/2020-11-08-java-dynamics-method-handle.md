---
layout: post
title: Java 动态性之方法句柄
category: Java
tags: [JVM, 动态性, 方法句柄]
keywords: JVM,动态性,方法句柄
excerpt: JSR-292 是 JVM 为动态类型支持而出现的规范，在 Java 7 中实现了这个规范，这个包的主要作用就在之前只能依赖符号引用来确定目标方法的基础上，增加了一种动态确定目标方法的机制，也就是方法句柄 MethodHandle。
---

在 Java 7 之前，JVM 字节码提供了如下 4 种字节码方法调用指令：

* invokevirtual：根据虚方法表调用虚方法

* invokespecial：调用实例构造方法，私有方法，父类继承方法

* invokeinteface：调用接口方法。

* invokestatic：调用静态方法

JVM 字节码指令集一直比较稳定，一直到 Java 7 中才增加了一个 invokedynamic 指令，这是 Java 为了实现『动态类型语言』支持而做的一种改进。但是在 Java 7 中并没有提供直接生成 invokedynamic 指令的方法，需要借助 ASM 这种底层字节码工具来产生 invokedynamic 指令。直到 Java 8 的 Lambda 表达式的出现，invokedynamic 指令的生成，在 Java 中才有了直接的生成方式。

## 动态类型语言和静态类型语言

动态类型语言和静态类型语言两者的区别就在于对类型的检查是在编译期还是在运行期，满足前者就是静态类型语言，反之是动态类型语言。说的在直白一点就是静态类型语言是判断变量自身的类型信息；动态类型语言是判断变量值的类型信息，变量没有类型信息，变量值才有类型信息，这是动态语言的一个重要特征。

Java 7 中增加的动态语言类型支持的本质是对 Java 虚拟机规范的修改，而不是对 Java 语言规则的修改，这一块相对来讲比较复杂，增加了虚拟机中的方法调用，最直接的受益者就是运行在 Java 平台的动态语言的编译器。

动态类型语言的支持对应的 JSR-292，主要包括两部份，一个是 Java 标准库中的新的方法调用 API，另一个是 Java 虚拟机规范中新增加的 invokedynamic 指令。

## java.lang.invoke 包

JSR-292 是 JVM 为动态类型支持而出现的规范，在 Java 7 中实现了这个规范，这个包的主要作用就在之前只能依赖符号引用来确定目标方法的基础上，增加了一种动态确定目标方法的机制，也就是方法句柄 MethodHandle。

## 方法句柄

方法句柄中包含两个重要的类，MethodType 和 MethodHandle。

**MethodType**

方法签名不可变对象，是对方法的一个映射，包含返回值和参数类型。在 lookup 时也是通过它来寻找的。每个方法句柄都有一个 MethodType 实例，用来指明方法的返回类型和参数类型。

**MethodHandle**

通过句柄我们可以直接调用该句柄所引用的底层方法。从作用上来看，方法句柄类似于反射中的 Method 类，但是方法句柄在调用时所提供的灵活性是 Method 类中的 invoke 方法所不能比的。

## 代码示例

```
import java.lang.invoke.MethodHandle;
import java.lang.invoke.MethodHandles;
import java.lang.invoke.MethodType;

public class MethodHandleSample {

    public static void main(final String[] args) throws Throwable {
        final MethodHandleSample methodHandleSample = new MethodHandleSample();

        final MethodType methodType = MethodType.methodType(void.class, String.class);
        final MethodHandle methodHandle = MethodHandles.lookup().findVirtual(MethodHandleSample.class, "print",
                methodType);
        methodHandle.invokeExact(methodHandleSample, "haha");
    }

    @SuppressWarnings("unused")
    private void print(final String s) {
        System.out.println(s);
    }
}
```

## 反射与方法句柄异同
* Reflection 和 MethodHandle 机制本质上都是在模拟方法调用，但是 Reflection 是在模拟 Java 代码层次的方法调用，而 MethodHandle 是在模拟字节码层次的方法调用。

* Reflection 中的 Method 对象远比 MethodHandle 机制中的 MethodHandle 对象所包含的信息要多。前者是方法在 Java 端的全面映像，包含了方法的签名、描述符以及方法属性表中各种属性的 Java 端表示方式，还包含有执行权限等的运行期信息。而后者仅仅包含着与执行该方法相关的信息。通俗的话说，Reflection 是重量级，而 MethodHandle 是轻量级。

* 由于 MethodHandle 是对字节码的方法指令调用的模拟，那理论上虚拟机在这方面做的各种优化（如方法内联），在 MethodHandle 上也应当可以采用类似思路去支持。而通过反射去调用方法则不行。

* Reflection API 的设计目标是只为 Java 语言服务的，而 MethodHandle 则设计为可服务于所有 Java 虚拟机之上的语言，其中也包括了 Java 语言。

## 反射与方法句柄性能比较

```
import java.lang.invoke.MethodHandle;
import java.lang.invoke.MethodHandles;
import java.lang.invoke.MethodType;
import java.lang.reflect.Method;
import java.time.Duration;
import java.time.Instant;
import java.util.Arrays;
import java.util.function.Consumer;
import java.util.stream.IntStream;

import org.junit.Test;

public class ReflectAndMethodHandleSample {

    @Test
    public void testBatchReflect() {
        // 预热
        IntStream.range(0, 10).forEach(i -> testReflect());

        // 测试
        final long[] batchReflectMillisArray = IntStream.range(0, 10).mapToLong(i -> testReflect()).toArray();

        System.out.println(String.format("Batch Reflect cost: %s", Arrays.toString(batchReflectMillisArray)));
        System.out.println(String.format("Batch Reflect average cost: %s",
                Arrays.stream(batchReflectMillisArray).average().getAsDouble()));
    }

    @Test
    public void testBatchMethodHandle() {
        // 预热
        IntStream.range(0, 10).forEach(i -> testMethodHandle());

        // 测试
        final long[] batchMethodHandleMillisArray = IntStream.range(0, 10).mapToLong(i -> testMethodHandle()).toArray();

        System.out.println(String.format("Batch MethodHandle cost: %s", Arrays.toString(batchMethodHandleMillisArray)));
        System.out.println(String.format("Batch MethodHandle average cost: %s",
                Arrays.stream(batchMethodHandleMillisArray).average().getAsDouble()));
    }

    private long testReflect() {
        try {
            final Method printMethod = getClass().getMethod("print", String.class);

            return test(m -> invokeReflect(m), printMethod);
        } catch (final Exception e) {
            e.printStackTrace();
        }

        return 0;
    }

    private long testMethodHandle() {
        final MethodType methodType = MethodType.methodType(void.class, String.class);

        try {
            final MethodHandle printMethodHandle = MethodHandles.lookup().findVirtual(getClass(), "print", methodType);

            return test(m -> invokeMethodHandle(m), printMethodHandle);
        } catch (final Exception e) {
            e.printStackTrace();
        }

        return 0;
    }

    private void invokeReflect(final Method printMethod) {
        try {
            printMethod.invoke(this, "haha");
        } catch (final Exception e) {
            e.printStackTrace();
        }
    }

    private void invokeMethodHandle(final MethodHandle printMethodHandle) {
        try {
            printMethodHandle.invokeExact(this, "haha");
        } catch (final Throwable e) {
            e.printStackTrace();
        }
    }

    private <T> long test(final Consumer<T> consumer, final T t) {
        final Instant start = Instant.now();
        IntStream.range(0, 1_000_000).forEach(i -> consumer.accept(t));
        final Instant end = Instant.now();

        final Duration duration = Duration.between(start, end);
        return duration.toMillis();
    }

    public void print(final String s) {
        System.out.println(s);
    }
}
```

**输出**
```
Batch Reflect cost: [5692, 5594, 5969, 5938, 5765, 5985, 5830, 5921, 5842, 5865]
Batch Reflect average cost: 5840.1

Batch MethodHandle cost: [5917, 5990, 5896, 6065, 5925, 6074, 6134, 5867, 5877, 6193]
Batch MethodHandle average cost: 5993.8
```

硬件：Intel Xeon Silver 4114 @ 2.20GHz 2.19GHz CPU，CPU 80G memory，Windows 10 OS

如上所述，执行 10 个批次，每个批次执行 print 方法 1 百万次。

总体上看，方法句柄的性能比反射略好。

## 参考

[https://blog.csdn.net/hj7jay/article/details/73480386](https://blog.csdn.net/hj7jay/article/details/73480386)

[https://www.cnblogs.com/wade-luffy/p/6058087.html](https://www.cnblogs.com/wade-luffy/p/6058087.html)

[https://www.cnblogs.com/tangliMeiMei/p/12983627.html](https://www.cnblogs.com/tangliMeiMei/p/12983627.html)

[https://nicky-chen.github.io/2018/08/22/java-methodhandler/](https://nicky-chen.github.io/2018/08/22/java-methodhandler/)
