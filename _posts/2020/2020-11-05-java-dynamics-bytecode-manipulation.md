---
layout: post
title: Java 动态性之字节码操作
category: Java
tags: [JVM, 动态性, 字节码操作]
keywords: JVM,动态性,字节码操作
excerpt: 运行时操作字节码可以让我们实现如下功能：动态生成新的类、动态改变某个类的结构。
---

运行时操作字节码可以让我们实现如下功能：

* 动态生成新的类

* 动态改变某个类的结构

## 常见的字节码操作类库

### BCEL

Byte Code Engineering Library (BCEL), 这是 Apache Software Foundation 的 Jakarta 项目的一部分。BCEL 是 Java classworking 广泛使用的一种框，它可以让您深入 JVM 汇编语言进行类操作的细节。

### ASM

ASM 是一个轻量级 Java 字节码操作框架，直接涉及量到 JVM 底层的操作和指令。

### CGLIB

CGLIB 是一个强大的，高性能，高质量的 Code 生成类库，基于 ASM 实现。

### Javassist

Javassist 是一个开源的分析、编辑和创建 Java 字节码的类库，性能较 ASM 差，跟 CGLIB 差不多，但是使用简单。

## Javassist 的 API

Javassist 最外层的 API 和 Java 的反射包中的 API 颇为类似。它主要有 CtClass，CtMethod，以及 CtField 及各类组成。用以执行和 JDK 反射 API 中 java.lang.Class，java.lang.reflect.Method，java.lang.reflect.Method.Field 相同的操作。

## 代码示例

```
import java.lang.reflect.Method;

import org.junit.Test;

import javassist.ClassPool;
import javassist.CtClass;
import javassist.CtConstructor;
import javassist.CtField;
import javassist.CtMethod;

public class JavassistSample {

    @Test
    public void testJavassist() throws Exception {
        final ClassPool classPool = ClassPool.getDefault();
        final CtClass ctClass = classPool.makeClass("org.javassist.A");

        // 创建属性
        final CtField ctField0 = CtField.make("private int id;", ctClass);
        final CtField ctField1 = CtField.make("private String name;", ctClass);
        ctClass.addField(ctField0);
        ctClass.addField(ctField1);

        // 创建方法
        final CtMethod ctMethod0 = CtMethod.make("public int getId() {return id;}", ctClass);
        final CtMethod ctMethod1 = CtMethod.make("public void setId(int id) {this.id = $1;}", ctClass);
        final CtMethod ctMethod2 = CtMethod.make("public String getName() {return name;}", ctClass);
        final CtMethod ctMethod3 = CtMethod.make("public void setName(String name) {this.name = $1;}", ctClass);
        ctClass.addMethod(ctMethod0);
        ctClass.addMethod(ctMethod1);
        ctClass.addMethod(ctMethod2);
        ctClass.addMethod(ctMethod3);

        // 添加构造器
        final CtConstructor ctConstructor = new CtConstructor(
                new CtClass[] { CtClass.intType, classPool.get(String.class.getName()) }, ctClass);
        ctConstructor.setBody("{this.id = $1; this.name = $2;}");
        ctClass.addConstructor(ctConstructor);

        // 生成字节码
        ctClass.writeFile("/tmp/classes");

        // 加载和执行生成的类
        final Class<?> clazz = ctClass.toClass();
        final Object object = clazz.getConstructor(int.class, String.class).newInstance(123, "abc");

        final Method getIdMethod = clazz.getMethod("getId");
        final Method getNameMethod = clazz.getMethod("getName");

        final Object id = getIdMethod.invoke(object);
        final Object name = getNameMethod.invoke(object);

        System.out.println(id);
        System.out.println(name);
    }
}
```

## 参考

[https://www.cnblogs.com/dooor/p/5289326.html](https://www.cnblogs.com/dooor/p/5289326.html)
