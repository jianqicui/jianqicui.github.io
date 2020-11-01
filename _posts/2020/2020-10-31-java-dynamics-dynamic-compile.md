---
layout: post
title: Java 动态性之动态编译
category: Java
tags: [JVM, 动态性, 动态编译]
keywords: JVM,动态性,动态编译
excerpt: 所谓动态编译，就是在程序运行时产生 Java 类，并编译成 class 文件。
---

所谓动态编译，就是在程序运行时产生 Java 类，并编译成 class 文件。

## 动态编译分类

动态编译分为两种：

* 写入文件

* 不写入文件

### 写入文件

```
import java.io.File;
import java.lang.reflect.Method;
import java.net.URL;
import java.net.URLClassLoader;
import java.nio.charset.StandardCharsets;
import java.util.Arrays;
import java.util.List;

import javax.tools.JavaCompiler;
import javax.tools.JavaFileObject;
import javax.tools.StandardJavaFileManager;
import javax.tools.ToolProvider;

import org.apache.commons.io.FileUtils;

public class DynamicCompileWithWritingFiles {

    public static void main(final String[] args) throws Exception {

        final String classString = "package org.sample;\n" +
                "\n" +
                "public class A {\n" +
                "\n" +
                "    private String fieldA;\n" +
                "\n" +
                "    public String getFieldA() {\n" +
                "        return fieldA;\n" +
                "    }\n" +
                "\n" +
                "    public void setFieldA(String fieldA) {\n" +
                "        this.fieldA = fieldA;\n" +
                "    }\n" +
                "}";

        final String javaFilePath = "/tmp/src/main/java/org/sample/A.java";
        FileUtils.write(new File(javaFilePath), classString, StandardCharsets.UTF_8);

        final String targetPath = "/tmp/target/classes/";
        final List<String> options = Arrays.asList("-d", targetPath);

        final JavaCompiler javaCompiler = ToolProvider.getSystemJavaCompiler();

        try (final StandardJavaFileManager standardJavaFileManager = javaCompiler.getStandardFileManager(null, null,
                null)) {
            final Iterable<? extends JavaFileObject> javaFileObjects = standardJavaFileManager
                    .getJavaFileObjects(javaFilePath);

            final JavaCompiler.CompilationTask task = javaCompiler.getTask(null, standardJavaFileManager, null, options,
                    null, javaFileObjects);

            task.call();
        }

        final URL[] urls = { new URL("file:".concat(targetPath)) };

        try (final URLClassLoader urlClassLoader = new URLClassLoader(urls)) {
            final Class<?> clazz = urlClassLoader.loadClass("org.sample.A");

            final Object object = clazz.newInstance();

            final Method setFieldAMethod = clazz.getMethod("setFieldA", String.class);

            setFieldAMethod.invoke(object, "123");

            final Method getFieldAMethod = clazz.getMethod("getFieldA");

            final Object result = getFieldAMethod.invoke(object);

            System.out.println(result);
        }
    }
}
```

### 不写入文件

```
import java.io.ByteArrayOutputStream;
import java.io.FilterOutputStream;
import java.io.IOException;
import java.io.OutputStream;
import java.lang.reflect.Method;
import java.net.URI;
import java.util.Arrays;

import javax.tools.FileObject;
import javax.tools.ForwardingJavaFileManager;
import javax.tools.JavaCompiler;
import javax.tools.JavaFileManager;
import javax.tools.JavaFileObject;
import javax.tools.SimpleJavaFileObject;
import javax.tools.StandardJavaFileManager;
import javax.tools.ToolProvider;

public class DynamicCompileWithoutWritingFile {

    public static void main(final String[] args) throws Exception {
        final String classString = "package org.sample;\n" +
                "\n" +
                "public class A {\n" +
                "\n" +
                "    private String fieldA;\n" +
                "\n" +
                "    public String getFieldA() {\n" +
                "        return fieldA;\n" +
                "    }\n" +
                "\n" +
                "    public void setFieldA(String fieldA) {\n" +
                "        this.fieldA = fieldA;\n" +
                "    }\n" +
                "}";

        final String className = "org.sample.A";

        final byte[] classBytes = getClassBytes(className, classString);
        final Class<?> clazz = getClass(className, classBytes);

        final Object object = clazz.newInstance();

        final Method setFieldAMethod = clazz.getMethod("setFieldA", String.class);

        setFieldAMethod.invoke(object, "123");

        final Method getFieldAMethod = clazz.getMethod("getFieldA");

        final Object result = getFieldAMethod.invoke(object);

        System.out.println(result);
    }

    private static byte[] getClassBytes(final String className, final String classString) throws Exception {
        final JavaCompiler javaCompiler = ToolProvider.getSystemJavaCompiler();
        final StandardJavaFileManager standardFileManager = javaCompiler.getStandardFileManager(null, null, null);

        try (final ClassJavaFileManager classJavaFileManager = new ClassJavaFileManager(standardFileManager)) {
            final StringJavaFileObject stringJavaFileObject = new StringJavaFileObject(className, classString);
            final JavaCompiler.CompilationTask compilationTask = javaCompiler.getTask(null, classJavaFileManager, null,
                    null, null, Arrays.asList(stringJavaFileObject));
            compilationTask.call();

            return classJavaFileManager.getClassBytes();
        }
    }

    private static Class<?> getClass(final String className, final byte[] classBytes) throws Exception {
        final BytesClassLoader bytesClassLoader = new BytesClassLoader(classBytes);

        return bytesClassLoader.loadClass(className);
    }

    private static URI toURI(final String className, final String extension) {
        final StringBuilder uriString = new StringBuilder();

        uriString.append("string:///");
        uriString.append(className.replace('.', '/'));
        uriString.append(extension);

        return URI.create(uriString.toString());
    }

    @SuppressWarnings("rawtypes")
    private static class ClassJavaFileManager extends ForwardingJavaFileManager {

        private byte[] classBytes;

        @SuppressWarnings("unchecked")
        public ClassJavaFileManager(final JavaFileManager fileManager) {
            super(fileManager);
        }

        @Override
        public JavaFileObject getJavaFileForOutput(final Location location, final String className,
                final JavaFileObject.Kind kind, final FileObject sibling) throws IOException {
            return new ClassJavaFileObject(className);
        }

        public byte[] getClassBytes() {
            return classBytes;
        }

        private class ClassJavaFileObject extends SimpleJavaFileObject {

            public ClassJavaFileObject(final String className) {
                super(toURI(className, JavaFileObject.Kind.CLASS.extension), JavaFileObject.Kind.CLASS);
            }

            @Override
            public OutputStream openOutputStream() throws IOException {
                return new FilterOutputStream(new ByteArrayOutputStream()) {
                    @Override
                    public void close() throws IOException {
                        out.close();
                        final ByteArrayOutputStream bos = (ByteArrayOutputStream) out;
                        classBytes = bos.toByteArray();
                    }
                };
            }
        }
    }

    private static class StringJavaFileObject extends SimpleJavaFileObject {

        private final String classString;

        public StringJavaFileObject(final String className, final String classString) {
            super(toURI(className, JavaFileObject.Kind.SOURCE.extension), JavaFileObject.Kind.SOURCE);
            this.classString = classString;
        }

        @Override
        public CharSequence getCharContent(final boolean ignoreEncodingErrors) throws IOException {
            return classString;
        }
    }

    private static class BytesClassLoader extends ClassLoader {

        private final byte[] classBytes;

        public BytesClassLoader(final byte[] classBytes) {
            this.classBytes = classBytes;
        }

        @Override
        protected Class<?> findClass(final String name) throws ClassNotFoundException {
            return defineClass(name, classBytes, 0, classBytes.length);
        }
    }
}
```

## 参考

[https://blog.csdn.net/zhao_xinhu/article/details/82499062](https://blog.csdn.net/zhao_xinhu/article/details/82499062)

[https://github.com/gam2046/UtilsClass/blob/master/locals/DynamicLoader.java](https://github.com/gam2046/UtilsClass/blob/master/locals/DynamicLoader.java)
