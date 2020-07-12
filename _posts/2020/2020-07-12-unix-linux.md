---
layout: post
title: UNIX和Linux的关系及区别
category: Linux
tags: [UNIX, Linux]
keywords: UNIX,Linux
excerpt: UNIX和Linux的关系及区别
---

UNIX与Linux之间的关系是一个很有意思的话题。在目前主流的操作系统中，UNIX诞生于20世纪60年代末，Windows诞生于20世纪80年代中期，Linux诞生于20世纪90年代初，可以说UNIX是操作系统中的"老大哥"，后来的Windows和Linux都参考了UNIX。

## UNIX历史

UNIX操作系统由贝尔实验室的肯•汤普森（Ken Thompson）和丹尼斯•里奇（Dennis Ritchie）发明。目标是开发一种交互式的、具有多道程序处理能力的分时操作系统，以取代当时广泛使用的批处理操作系统。

## Linux往事

Linux内核最初是由李纳斯•托瓦兹（Linus Torvalds）在赫尔辛基大学读书时出于个人爱好而编写的，当时他觉得教学用的迷你版UNIX操作系统Minix太难用了，于是决定自己开发一个操作系统。第1版本于1991年9月发布，当时仅有10000行代码。

李纳斯•托瓦兹没有保留Linux源代码的版权，公开了代码，并邀请他人一起完善Linux。与Windows及其他有专利权的操作系统不同，Linux开放源代码，任何人都可以免费使用它。

## UNIX与Linux的关系

二者的关系，不是大哥和小弟，"UNIX是Linux的父亲"这个说法更怡当。之所以要介绍它们的关系，是因为要告诉读者，在学习的时候，其实Linux与UNIX有很多的共通之处，简单地说，如果你已经熟练掌握了Linux，那么再上手使用UNIX会非常容易。

二者也有两个大的区别：

* UNIX系统大多是与硬件配套的，也就是说，大多数UNIX系统如AIX、HP-UX等是无法安装在x86服务器和个人计算机上的，而Linux则可以运行在多种硬件平台上。

* UNIX是商业软件，而Linux是开源软件，是免费、公开源代码的。

## UNIX/Linux系统结构

UNIX/Linux系统可以地抽象为3个层次。底层是UNIX/Linux操作系统，即系统内核（Kernel）；中间层是Shell层，即命令解释层；高层则是应用层。

![](/assets/images/2020/architecture_unix_linux.jpg)

### 内核层
内核层是UNIX/Linux系统的核心和基础，它直接附着在硬件平台之上，控制和管理系统内各种资源（硬件资源和软件资源），有效地组织进程的运行，从而扩展硬件的功能，提高资源的利用效率，为用户提供方便、高效、安全、可靠的应用环境。

### Shell层
Shell层是与用户直接交互的界面。用户可以在提示符下输入命令行，由Shell解释执行并输出相应结果或者有关信息，所以我们也把Shell称作命令解释器，利用系统提供的丰富命令可以快捷而简便地完成许多工作。

### 应用层
应用层提供基于X Window协议的图形环境。X Window协议定义了一个系统所必须具备的功能，系统能满足此协议，便可称为X Window。

X Window与微软的Windows图形环境有很大的区别：

* UNIX/Linux系统与X Window没有必然捆绑的关系，也就是说，UNIX/Linux可以安装X Window，也可以不安装；而微软的 Windows图形环境与内核捆绑密切。

* UNIX/Linux系统不依赖图形环境，依然可以通过命令行完成 100% 的功能，而且因为不使用图形环境还会节省大量的系统资源。

## 参考

[http://c.biancheng.net/view/707.html](http://c.biancheng.net/view/707.html)
