---
layout: post
title: 程序容错模式
category: Software Engineering
tags: [程序, 容错, 模式]
keywords: 程序,容错,模式
excerpt: 程序容错模式
---

## Failover

Failover 是指失败后立即重试。适用于同步数据、查询第三方数据等业务上幂等的场景。

## Failback

Failback 是指失败后记录失败日志。适用于对账、补偿等场景，常常和 failover 同时使用：失败了先记录下来用专门的服务去重试，或者就地重试，失败了再记录下来。

## Failfast

Failfast 是指基于当前的数据、状态等因素，判断出之后肯定会失败或异常的情况下，直接抛出异常。

## Failsafe

Failsafe 是指如果有异常，不抛出异常，不中断程序，继续执行下去。在一些第三方调用场景、配置异常返回默认值的场景常常会用到。
