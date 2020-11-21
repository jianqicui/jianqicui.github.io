---
layout: post
title: 在 Log 中 使用和不适用占位符的差异
category: Log
tags: [Log, 占位符]
keywords: Log,占位符
excerpt: 传递参数给 Log，大概有两种方式，使用占位符 "{}" 和使用字符串拼接 "+"。哪种才是最优的方式呢？文中用数据来说明。
---

传递参数给 Log，大概有两种方式。

* 使用占位符 "{}"

* 和使用字符串拼接 "+"

哪种才是最优的方式呢？文中用数据来说明。

## 测试代码

**pom.xml**

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org</groupId>
    <artifactId>logback-sample</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <maven.compiler.target>1.8</maven.compiler.target>
        <maven.compiler.source>1.8</maven.compiler.source>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.30</version>
        </dependency>

        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.2.3</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.1</version>
        </dependency>
    </dependencies>
</project>
```

**logback.xml**

```
<?xml version="1.0" encoding="UTF-8"?>

<configuration>
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <root level="INFO">
        <appender-ref ref="STDOUT" />
    </root>
</configuration>
```

**LogbackSample**

```
import java.time.Duration;
import java.time.Instant;
import java.util.Arrays;
import java.util.concurrent.Callable;
import java.util.stream.IntStream;

import org.junit.Test;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class LogbackSample {

    private static final Logger logger = LoggerFactory.getLogger(LogbackSample.class);

    @Test
    public void testBatchExecutingLogsWithPlaceholder() {
        final String[] messages = getMessages();

        // Warm up
        Arrays.stream(messages).forEach(message -> testExecutingLogsWithPlaceholder(message));

        // Test
        final long[] batchExecutingLogsWithPlaceholderMillisArray = Arrays.stream(messages)
                .mapToLong(message -> testExecutingLogsWithPlaceholder(message)).toArray();

        System.out.println(String.format("Batch executing logs with placeholder cost: %s",
                Arrays.toString(batchExecutingLogsWithPlaceholderMillisArray)));
        System.out.println(String.format("Batch executing logs with placeholder average cost: %s",
                Arrays.stream(batchExecutingLogsWithPlaceholderMillisArray).average().getAsDouble()));
    }

    @Test
    public void testBatchExecutingLogsWithoutPlaceholder() {
        final String[] messages = getMessages();

        // Warm up
        Arrays.stream(messages).forEach(message -> testExecutingLogsWithoutPlaceholder(message));

        // Test
        final long[] batchExecutingLogsWithoutPlaceholderMillisArray = Arrays.stream(messages)
                .mapToLong(message -> testExecutingLogsWithoutPlaceholder(message)).toArray();

        System.out.println(String.format("Batch executing logs without placeholder cost: %s",
                Arrays.toString(batchExecutingLogsWithoutPlaceholderMillisArray)));
        System.out.println(String.format("Batch executing logs without placeholder average cost: %s",
                Arrays.stream(batchExecutingLogsWithoutPlaceholderMillisArray).average().getAsDouble()));
    }

    @Test
    public void testBatchSkippingLogsWithPlaceholder() {
        final String[] messages = getMessages();

        // Warm up
        Arrays.stream(messages).forEach(message -> testSkippingLogsWithPlaceholder(message));

        // Test
        final long[] batchSkippingLogsWithPlaceholderMillisArray = Arrays.stream(messages)
                .mapToLong(message -> testSkippingLogsWithPlaceholder(message)).toArray();

        System.out.println(String.format("Batch skipping logs with placeholder cost: %s",
                Arrays.toString(batchSkippingLogsWithPlaceholderMillisArray)));
        System.out.println(String.format("Batch skipping logs with placeholder average cost: %s",
                Arrays.stream(batchSkippingLogsWithPlaceholderMillisArray).average().getAsDouble()));
    }

    @Test
    public void testBatchSkippingLogsWithoutPlaceholder() {
        final String[] messages = getMessages();

        // Warm up
        Arrays.stream(messages).forEach(message -> testSkippingLogsWithoutPlaceholder(message));

        // Test
        final long[] batchSkippingLogsWithoutPlaceholderMillisArray = Arrays.stream(messages)
                .mapToLong(message -> testSkippingLogsWithoutPlaceholder(message)).toArray();

        System.out.println(String.format("Batch skipping logs without placeholder cost: %s",
                Arrays.toString(batchSkippingLogsWithoutPlaceholderMillisArray)));
        System.out.println(String.format("Batch skipping logs without placeholder average cost: %s",
                Arrays.stream(batchSkippingLogsWithoutPlaceholderMillisArray).average().getAsDouble()));
    }

    private String[] getMessages() {
        return IntStream.range(0, 5).mapToObj(i -> "message" + i).toArray(String[]::new);
    }

    private long testExecutingLogsWithPlaceholder(final String message) {
        return testLogs(() -> {
            logger.info("Message: {}", message);

            return null;
        });
    }

    private long testExecutingLogsWithoutPlaceholder(final String message) {
        return testLogs(() -> {
            logger.info("Message: " + message);

            return null;
        });
    }

    private long testSkippingLogsWithPlaceholder(final String message) {
        return testLogs(() -> {
            logger.debug("Message: {}", message);

            return null;
        });
    }

    private long testSkippingLogsWithoutPlaceholder(final String message) {
        return testLogs(() -> {
            logger.debug("Message: " + message);

            return null;
        });
    }

    private long testLogs(final Callable<Void> callable) {
        final Instant start = Instant.now();
        IntStream.range(0, 1_000_000).forEach(i -> {
            try {
                callable.call();
            } catch (final Exception e) {
                e.printStackTrace();
            }
        });
        final Instant end = Instant.now();

        final Duration duration = Duration.between(start, end);
        return duration.toMillis();
    }
}
```

## 测试输出

**硬件**

CPU: Intel Xeon Silver 4114 @ 2.20GHz 2.19GHz

Memory: 80G

Disk: SSD 512G

OS: Windows 10

JDK: Oracle JDK 8

每个测试有 5 个批次，每个批次跑 1 百万次。

```
Batch executing logs with placeholder cost: [15937, 15344, 15594, 15312, 15557]
Batch executing logs with placeholder average cost: 15548.8

Batch executing logs without placeholder cost: [15578, 15500, 15437, 15750, 15501]
Batch executing logs without placeholder average cost: 15553.2

Batch skipping logs with placeholder cost: [0, 0, 0, 0, 16]
Batch skipping logs with placeholder average cost: 3.2

Batch skipping logs without placeholder cost: [43, 18, 13, 16, 15]
Batch skipping logs without placeholder average cost: 21.0
```

## 总结

* 在 Log 被执行的情况下，使用和不使用占位符的耗时差不多。

* 在 Log 被跳过的情况下，使用占位符的耗时大大少于不使用的耗时。这是因为字符串拼接总是会被执行。

* 推荐使用占位符 "{}" 方式。
