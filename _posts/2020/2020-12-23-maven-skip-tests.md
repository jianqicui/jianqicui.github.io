---
layout: post
title: Maven 中 -DskipTests=true 和 -Dmaven.test.skip=true 的区别
category: Maven
tags: [Maven, skipTests, maven.test.skip]
keywords: Maven,skipTests,maven.test.skip
excerpt: Maven 中 -DskipTests=true 和 -Dmaven.test.skip=true，都可以用于跳过单元测试，那它们的区别是什么呢？
---

Maven 中 -DskipTests=true 和 -Dmaven.test.skip=true，都可以用于跳过单元测试，那它们的区别是什么呢？

## -DskipTests=true

不执行测试用例，但编译测试用例类生成相应的 class 文件至 target/test-classes 下。

### 在 pom.xml 中的设置

```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.5</version>
    <configuration>
        <skipTests>true</skipTests>
    </configuration>
</plugin>
```

## -Dmaven.test.skip=true

不执行测试用例，也不编译测试用例类，不但跳过单元测试的运行，也跳过测试代码的编译。

### 在 pom.xml 中的设置

```
<plugin>
    <groupId>org.apache.maven.plugin</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>2.1</version>
    <configuration>
        <skip>true</skip>
    </configuration>
</plugin>
```

## 参考

[https://iogogogo.github.io/2020/08/20/maven-skip-test/](https://iogogogo.github.io/2020/08/20/maven-skip-test/)
