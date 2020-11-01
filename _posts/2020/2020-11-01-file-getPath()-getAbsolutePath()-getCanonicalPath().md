---
layout: post
title: File 类的 getPath()，getAbsolutePath()，getCanonicalPath() 区别
category: Java
tags: [Java, File]
keywords: Java,File
excerpt: File 类的 getPath()，getAbsolutePath()，getCanonicalPath() 区别
---

## getPath()

gets the path string that the File object was constructed with, and it may be relative current directory.

返回的是定义时的路径，可能是相对路径，也可能是绝对路径，这个取决于定义时用的是相对路径还是绝对路径。如果定义时用的是绝对路径，那么使用 getPath() 返回的结果跟用 getAbsolutePath() 返回的结果一样。

## getAbsolutePath()

gets the path string after resolving it against the current directory if it's relative, resulting in a fully qualified path.

返回的是定义时的路径对应的相对路径，但不会处理 “.” 和 “..” 的情况。

## getCanonicalPath()

gets the path string after resolving any relative path against current directory, and removes any relative pathing (. and ..), and any file system links to return a path which the file system considers the canonical means to reference the file system object to which it points.

返回的是规范化的绝对路径，相当于将 getAbsolutePath() 中的 “.” 和 “..” 解析成对应的正确的路径。

## 代码示例

```
File file = new File("./test.txt");
System.out.println(file.getPath()); // ./test.txt
System.out.println(file.getAbsolutePath()); // /tmp/Test/./test.txt
System.out.println(file.getCanonicalPath()); // /tmp/Test/test.txt
```

## 参考

[https://www.bbsmax.com/A/A2dmAEbden/](https://www.bbsmax.com/A/A2dmAEbden/)

[https://my.oschina.net/u/2308739/blog/666426](https://my.oschina.net/u/2308739/blog/666426)
