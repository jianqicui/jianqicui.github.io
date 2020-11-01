---
layout: post
title: Class 类 getName()、getCanonicalName()、getSimpleName()、getTypeName() 方法的异同
category: Java
tags: [Java, Class]
keywords: Java,Class
excerpt: Class 类 getName()、getCanonicalName()、getSimpleName()、getTypeName() 方法的异同
---

## getName()

返回该类对象作为字符串表示的实体（类、接口、数组类、基本数据类型或 void）的名称

可以理解为返回的是虚拟机中 Class 对象的表示

当动态加载类的时候，会用到该方法的返回值，如: 使用 Class.forName() 方法

如果是内部类，则使用 $ 符号进行连接

如果是数组，则使用 [ 来表示，数组是几维，[ 就有几个

## getCanonicalName()

返回 Java 语言规范定义的底层类的规范名称

如果是一个本地类或匿名类或其组件的数组类型没有规范名称的，则返回 null

可以理解为返回的是正常的包含路径的类名

该方法的返回值可以在 import 语句中使用

能唯一标识一个类，还可以在 toString() 或日志操作期间使用

## getSimpleName()

返回源代码中给出的基础类的简单名称，如果基础类是匿名的，则返回空字符串

可以理解为返回的是仅仅是类名，不包含路径

不能保证能唯一标识一个类，可以在 toString() 或日志操作期间使用

## getTypeName()

返回此类型名称的信息字符串

可以理解为返回的是该类类型的名称

## 代码示例

```
package lang.reflect;

public class AAA {

    class BBB {

    }

    public static void main(String[] args) {
        // 数组
        System.out.println(String[].class.getName()); // [Ljava.lang.String;
        System.out.println(String[].class.getCanonicalName()); // java.lang.String[]
        System.out.println(String[].class.getSimpleName()); // String[]
        System.out.println(String[].class.getTypeName()); // java.lang.String[]

        // 成员内部类
        System.out.println(BBB.class.getName()); // lang.reflect.AAA$BBB
        System.out.println(BBB.class.getCanonicalName()); // lang.reflect.AAA.BBB
        System.out.println(BBB.class.getSimpleName()); // BBB
        System.out.println(BBB.class.getTypeName()); // lang.reflect.AAA$BBB

        // 匿名内部类
        System.out.println(new Object() {
        }.getClass().getName()); // lang.reflect.AAA$1
        System.out.println(new Object() {
        }.getClass().getCanonicalName()); // null
        System.out.println(new Object() {
        }.getClass().getSimpleName()); // ""
        System.out.println(new Object() {
        }.getClass().getTypeName()); // lang.reflect.AAA$4

        // 普通类
        System.out.println(AAA.class.getName()); // lang.reflect.AAA
        System.out.println(AAA.class.getCanonicalName()); // lang.reflect.AAA
        System.out.println(AAA.class.getSimpleName()); // AAA
        System.out.println(AAA.class.getTypeName()); // lang.reflect.AAA

        // 基本数据类型
        System.out.println(int.class.getName()); // int
        System.out.println(int.class.getCanonicalName()); // int
        System.out.println(int.class.getSimpleName()); // int
        System.out.println(int.class.getTypeName()); // int
    }
}
```

## 参考

[https://blog.csdn.net/qq_27788177/article/details/108606481](https://blog.csdn.net/qq_27788177/article/details/108606481)

[https://blog.csdn.net/Goodbye_Youth/article/details/83536840](https://blog.csdn.net/Goodbye_Youth/article/details/83536840)
