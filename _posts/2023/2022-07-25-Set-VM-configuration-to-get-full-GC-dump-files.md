---
layout: post
title: Set VM configuration to get full GC dump files
category: Java
tags: [VM configuration, full GC dump]
keywords: VM configuration,full GC dump
excerpt: Set VM configuration to get full GC dump files
---

## Background

If full GC is frequently made in a service, it will impact availability and performance, the root cause must be identified.

Using VM configuration, e.g, `PrintGCDetails`, `PrintHeapAtGC`, the detail log will be printed in gc log file.

But as `full` gc is the most focused, there are 2 points which are not fit for troubleshooting `full` gc.

* The format of gc log file is text, which is not GUI type.
* All gc logs, including full gc, are printed in 1 log file. Full gc logs are hard to be found.

## Set configuration in static way

Add below in VM configuration

```
-XX:+HeapDumpOnOutOfMemoryError
-XX:+HeapDumpBeforeFullGC
-XX:+HeapDumpAfterFullGC
-XX:HeapDumpPath=/tmp/heap-dump/
```

## Set configuration in dynamic way

[jinfo](https://docs.oracle.com/javase/7/docs/technotes/tools/share/jinfo.html) prints and set Java configuration information for a given Java process.

```
// Print all VM flags
jinfo -flags 25070

// Print VM flag by name
jinfo -flag HeapDumpOnOutOfMemoryError 25070
jinfo -flag HeapDumpBeforeFullGC 25070
jinfo -flag HeapDumpAfterFullGC 25070
jinfo -flag HeapDumpPath 25070

// Set(Enable) VM flag by name
jinfo -flag +HeapDumpOnOutOfMemoryError 25070
jinfo -flag +HeapDumpBeforeFullGC 25070
jinfo -flag +HeapDumpAfterFullGC 25070

// Set(Disable) VM flag by name
jinfo -flag -HeapDumpOnOutOfMemoryError 25070
jinfo -flag -HeapDumpBeforeFullGC 25070
jinfo -flag -HeapDumpAfterFullGC 25070

// Set the value of VM flag by name
jinfo -flag HeapDumpPath=/tmp/heap-dump/ 25070
```

## Full GC dump files generation

If above confiiguration has been made, the `before/after` full gc dump files will be generated in `HeapDumpPath`.

These 2 dump files can be downloaded, then analysed in VM tools, e.g, `jvisualvm`, `MAT`.
