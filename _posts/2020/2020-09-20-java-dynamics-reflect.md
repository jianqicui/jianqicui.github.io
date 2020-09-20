---
layout: post
title: Java 动态性之反射
category: Java
tags: [JVM, 动态性, 反射]
keywords: JVM,动态性,反射
excerpt: Java 反射机制是在运行状态中，对于任意一个类，都能够获取这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取类信息以及动态调用对象内容就称为 Java 语言的反射机制。
---

Java 反射机制是在运行状态中，对于任意一个类，都能够获取这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取类信息以及动态调用对象内容就称为 Java 语言的反射机制。

简单说，反射机制给 Java 提供了一定的动态特性，让程序在运行时能够获取自身的信息，只要给定类的名字，那么就可以通过反射机制来获得类的所有信息（方法和属性）。

## 反射机制主要提供的功能

* 在运行时判断任意一个对象所属的类。

* 在运行时构造任意一个类的对象。

* 在运行时判断任意一个类所具有的成员变量和方法。

* 在运行时调用任意一个对象的方法。

## 反射机制的相关类

* Class 类：代表一个类，位于 java.lang 包下

* Field 类：代表类的成员变量（成员变量也称为类的属性）

* Method 类：代表类的方法

* Constructor 类：代表类的构造方法

* Array 类：提供了动态创建数组，以及访问数组的元素的静态方法

### Class 类

Class 代表类的实体，在运行的 Java 应用程序中表示类和接口。在这个类中提供了很多有用的方法，这里对他们简单的分类介绍。

**获得类相关的方法**

| 方法 | 用途 |
| ---- | ---- |
| asSubclass(Class\<U\> clazz) | 把传递的类的对象转换成代表其子类的对象 |
| Cast | 把对象转换成代表类或是接口的对象 |
| getClassLoader() | 获得类的加载器 |
| getClasses() | 返回一个数组，数组中包含该类中所有公共类和接口类的对象 |
| getDeclaredClasses() | 返回一个数组，数组中包含该类中所有类和接口类的对象 |
| forName(String className) | 根据类名返回类的对象 |
| getName() | 获得类的完整路径名字 |
| newInstance() | 创建类的实例 |
| getPackage() | 获得类的包 |
| getSimpleName() | 获得类的名字 |
| getSuperclass() | 获得当前类继承的父类的名字 |
| getInterfaces() | 获得当前类实现的类或是接口 |

**获得类中属性相关的方法**

| 方法 | 用途 |
| ---- | ---- |
| getField(String name) | 获得某个公有的属性对象 |
| getFields() | 获得所有公有的属性对象 |
| getDeclaredField(String name) | 获得某个属性对象 |
| getDeclaredFields() | 获得所有属性对象 |

**获得类中注解相关的方法**

| 方法 | 用途 |
| ---- | ---- |
| getAnnotation(Class\<A\> annotationClass) | 返回该类中与参数类型匹配的公有注解对象 |
| getAnnotations() | 返回该类所有的公有注解对象 |
| getDeclaredAnnotation(Class\<A\> annotationClass) | 返回该类中与参数类型匹配的所有注解对象 |
| getDeclaredAnnotations() | 返回该类所有的注解对象 |

**获得类中构造器相关的方法**

| 方法 | 用途 |
| ---- | ---- |
| getConstructor(Class...\<?\> parameterTypes) | 获得该类中与参数类型匹配的公有构造方法 |
| getConstructors() | 获得该类的所有公有构造方法 |
| getDeclaredConstructor(Class...\<?\> parameterTypes) | 获得该类中与参数类型匹配的构造方法 |
| getDeclaredConstructors() |	获得该类所有构造方法 |

**获得类中方法相关的方法**

| 方法 | 用途 |
| ---- | ---- |
| getMethod(String name, Class...\<?\> parameterTypes) | 获得该类某个公有的方法 |
| getMethods() | 获得该类所有公有的方法 |
| getDeclaredMethod(String name, Class...\<?\> parameterTypes) | 获得该类某个方法 |
| getDeclaredMethods() | 获得该类所有方法 |

**类中其他重要的方法**

| 方法 | 用途 |
| ---- | ---- |
| isAnnotation() | 如果是注解类型则返回 true |
| isAnnotationPresent(Class\<? extends Annotation\> annotationClass) | 如果是指定类型注解类型则返回 true |
| isAnonymousClass() | 如果是匿名类则返回 true |
| isArray() | 如果是一个数组类则返回 true |
| isEnum() | 如果是枚举类则返回 true |
| isInstance(Object obj) | 如果 obj 是该类的实例则返回 true |
| isInterface() | 如果是接口类则返回 true |
| isLocalClass() | 如果是局部类则返回 true |
| isMemberClass() | 如果是内部类则返回 true |

### Field 类

Field 代表类的成员变量（成员变量也称为类的属性）。

| 方法 | 用途 |
| ---- | ---- |
| equals(Object obj) | 属性与 obj 相等则返回 true |
| get(Object obj) | 获得 obj 中对应的属性值 |
| set(Object obj, Object value) | 设置 obj 中对应属性值 |

### Method 类

Method 代表类的方法。

| 方法 | 用途 |
| ---- | ---- |
| invoke(Object obj, Object... args) | 传递 object 对象及参数调用该对象对应的方法 |

### Constructor 类

Constructor 代表类的构造方法。

| 方法 | 用途 |
| ---- | ---- |
| newInstance(Object... initargs) | 根据传递的参数创建类的对象 |

### Array 类

Array 代表数组。

| 方法 | 用途 |
| ---- | ---- |
| newInstance(Class\<?\> componentType, int length) | 根据长度创建单维数组 |
| newInstance(Class<?> componentType, int... dimensions) | 根据维度创建多维数组 |
| getLength(Object array) | 获得数组长度 |
| get(Object array, int index) | 根据索引获得数组对象 |

### 代码示例

* ReflectTest 测试反射机制的类

* User 实体类

```
public class ReflectTest {

    private static void test() throws Exception {

        String path = "com.test.reflect.User";

        // 获取类信息的方式有三种，三种方式获取的类对象相同；clazz是以后操作的关键
        // 第一种方式
        Class clazz = Class.forName(path);
        // 第二种方式
        Class clazz1 = User.class;
        // 第三种方式
        User u = new User();
        Class clazz2 = u.getClass();

        System.out.println(clazz.hashCode());
        System.out.println(clazz1.hashCode());
        System.out.println(clazz2.hashCode());

        // 获取类名称包名+类名；
        String className = clazz.getName(); // clazz.getSimpleName();    // 只有类名

        // 获取clazz构造方法
        // 利用无参的构造方法创建对象user, 比较常用
        User user = (User) clazz.newInstance();
        user.setName("张三");
        // 获取有参的构造方法
        Constructor<User> constructor = clazz.getConstructor(new Class[]{int.class, String.class});
        // 根据获取的有参的构造方法创建一个对象
        User user2 = constructor.newInstance(1001, "李四");
        System.out.println(user.getName()+"---------"+user2.getName());


        // 对属性的操作
        // 这样只能获取public的属性
        Field[] fields = clazz.getFields();
        // 获取所有的属性  declared：公告的
        Field[] fields1 = clazz.getDeclaredFields();  
        // 根据属性的名字获取属性
        Field field = clazz.getDeclaredField("name");
        // 跳过检查，不然给私有变量赋值会报错，也会提高效率，建议使用
        field.setAccessible(true);
        field.set(user, "王五");
        System.out.println(user.getName());


        // 对方法的操作
        // 获取所有的公共方法
        Method[] methods = clazz.getMethods();
        // 获取所有的方法
        Method[] methods1 = clazz.getDeclaredMethods();
        // 根据指定的名字获取方法， public类型的方法
        Method method = clazz.getMethod("hello", String.class);
        // 跳过验证 ，提高效率
        method.setAccessible(true);
        Object result = method.invoke(user, "调用方法");
        System.out.println(result);
    }

    public static void main(String[] args) throws Exception {
        test();
    }
}

public class User {

    private int id;
    private String name;

    // 无参的构造方法，容易被忘记
    public User(){

    }

    //有参的构造方法
    public User(int id, String name){
        this.id = id;
        this.name = name;
    }

    // 类中一个普通的方法
    public String hello(String name){
        System.out.println("hello:"+ name);
        return "方法调用成功";
    }

    public int getId() {
        return id;
    }
    public void setId(int id) {
        this.id = id;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
}
```

## 反射应用

反射是 Java 框架的灵魂技术，很多框架都使用了反射技术，如 Spring，Mybatis，Hibernate 等。

### JDBC 的数据库的连接

在 JDBC 连接数据库中，一般包括加载驱动，获得数据库连接等步骤。而加载驱动，就是引入相关 Jar 包后，通过 Class.forName() 即反射技术，加载数据库的驱动程序。

### Spring 框架的使用

Spring 通过 XML 配置模式装载 Bean，也是反射的一个典型例子。

装载过程：

* 将程序内 XML 配置文件加载入内存中

* Java 类解析 XML 里面的内容，得到相关字节码信息

* 使用反射机制，得到 Class 实例

* 动态配置实例的属性，使用

这样做当然是有好处的：不用每次都去 new 实例了，并且可以修改配置文件，比较灵活。

## 反射存在的问题

### 性能问题

Java 反射的性能并不好，原因主要是编译器没法对反射相关的代码做优化。

### 安全问题

我们知道单例模式的设计过程中，会强调将构造器设计为私有，因为这样可以防止从外部构造对象。但是反射可以获取类中的域、方法、构造器，修改访问权限。所以这样并不一定是安全的。

## 反射调用的优化

### 选择合适的 API

选择合适的 API 主要是在获取反射相关元数据的时候尽量避免使用遍历的方法，例如：

* 获取 Field 实例：尽量避免频繁使用 Class#getDeclaredFields() 或者 Class#getFields()，应该根据 Field 的名称直接调用 Class#getDeclaredField() 或者 Class#getField()。

* 获取 Method 实例：尽量避免频繁使用 Class#getDeclaredMethods() 或者 Class#getMethods()，应该根据 Method 的名称和参数类型数组调用 Class#getDeclaredMethod() 或者 Class#getMethod()。

* 获取 Constructor 实例：尽量避免频繁使用 Class#getDeclaredConstructors() 或者 Class#getConstructors()，应该根据 Constructor 参数类型数组调用 Class#getDeclaredConstructor() 或者 Class#getConstructor()。

其实思路很简单，除非我们想要获取 Class 的所有 Field、Method 或者 Constructor，否则应该避免使用返回一个集合或者数组的 API，这样子能减少遍历或者判断带来的性能损耗。

### 缓存反射操作相关元数据

使用缓存机制缓存反射操作相关元数据的原因是因为反射操作相关元数据的实时获取是比较耗时的，这里列举几个相对耗时的场景：

* 获取 Class 实例：Class#forName()，此方法可以查看源码，耗时相对其他方法高得多。

* 获取 Field 实例：Class#getDeclaredField()、Class#getDeclaredFields()、Class#getField()、Class#getFields()。

* 获取 Method 实例：Class#getDeclaredMethod()、Class#getDeclaredMethods()、Class#getMethod()、Class#getMethods()。

* 获取 Constructor 实例：Class#getDeclaredConstructor()、Class#getDeclaredConstructors()、Class#getConstructor()、Class#getConstructors()。

这里举个简单的例子，需要反射调用一个普通 JavaBean 的 Setter 和 Getter 方法：

```
public class JavaBean {

    private String name;

    ...
}

public class Main {

    private static final Map<Class<?>, List<ReflectionMetadata>> METADATA = new HashMap<>();
    private static final Map<String, Class<?>> CLASSES = new HashMap<>();

    // 解析的时候尽量放在<cinit>里面
    static {
        Class<?> clazz = JavaBean.class;
        CLASSES.put(clazz.getName(), clazz);
        List<ReflectionMetadata> metadataList = new ArrayList<>();
        METADATA.put(clazz, metadataList);
        try {
            for (Field f : clazz.getDeclaredFields()) {
                ReflectionMetadata metadata = new ReflectionMetadata();
                metadataList.add(metadata);
                metadata.setTargetClass(clazz);
                metadata.setField(f);
                String name = f.getName();
                Class<?> type = f.getType();
                metadata.setReadMethod(clazz.getDeclaredMethod(String.format("get%s%s", Character.toUpperCase(name.charAt(0)), name.substring(1))));
                metadata.setWriteMethod(clazz.getDeclaredMethod(String.format("set%s%s", Character.toUpperCase(name.charAt(0)), name.substring(1)), type));
            }
        } catch (Exception e) {
            throw new IllegalStateException(e);
        }
    }

    public static void main(String[] args) throws Exception {
        String fieldName = "name";
        Class<JavaBean> javaBeanClass = JavaBean.class;
        JavaBean javaBean = new JavaBean();
        invokeSetter(javaBeanClass, javaBean, fieldName , "Doge");
        System.out.println(invokeGetter(javaBeanClass,javaBean, fieldName));
        invokeSetter(javaBeanClass.getName(), javaBean, fieldName , "Throwable");
        System.out.println(invokeGetter(javaBeanClass.getName(),javaBean, fieldName));
    }

    private static void invokeSetter(String className, Object target, String fieldName, Object value) throws Exception {
        METADATA.get(CLASSES.get(className)).forEach(each -> {
            Field field = each.getField();
            if (field.getName().equals(fieldName)) {
                try {
                    each.getWriteMethod().invoke(target, value);
                } catch (Exception e) {
                    throw new IllegalStateException(e);
                }
            }
        });
    }

    private static void invokeSetter(Class<?> clazz, Object target, String fieldName, Object value) throws Exception {
        METADATA.get(clazz).forEach(each -> {
            Field field = each.getField();
            if (field.getName().equals(fieldName)) {
                try {
                    each.getWriteMethod().invoke(target, value);
                } catch (Exception e) {
                    throw new IllegalStateException(e);
                }
            }
        });
    }

    private static Object invokeGetter(String className, Object target, String fieldName) throws Exception {
        for (ReflectionMetadata metadata : METADATA.get(CLASSES.get(className))) {
            if (metadata.getField().getName().equals(fieldName)) {
                return metadata.getReadMethod().invoke(target);
            }
        }
        throw new IllegalStateException();
    }

    private static Object invokeGetter(Class<?> clazz, Object target, String fieldName) throws Exception {
        for (ReflectionMetadata metadata : METADATA.get(clazz)) {
            if (metadata.getField().getName().equals(fieldName)) {
                return metadata.getReadMethod().invoke(target);
            }
        }
        throw new IllegalStateException();
    }

    private static class ReflectionMetadata {

        private Class<?> targetClass;
        private Field field;
        private Method readMethod;
        private Method writeMethod;

        ...
    }
}
```

简单来说，解析反射元数据进行缓存的操作最好放在静态代码块或者首次调用的时候（也就是懒加载），这样能够避免真正调用的时候总是需要重新加载一次反射相关元数据。

## 参考

[https://www.cnblogs.com/DEADF1SH-CAT/p/12563526.html](https://www.cnblogs.com/DEADF1SH-CAT/p/12563526.html)

[http://blog.qiji.tech/archives/4374](http://blog.qiji.tech/archives/4374)

[https://www.jianshu.com/p/9be58ee20dee](https://www.jianshu.com/p/9be58ee20dee)

[https://juejin.im/post/6844903965725818887](https://juejin.im/post/6844903965725818887)

[https://www.lagou.com/lgeduarticle/85248.html](https://www.lagou.com/lgeduarticle/85248.html)

[https://www.cnblogs.com/throwable/p/12272290.html](https://www.cnblogs.com/throwable/p/12272290.html)

[https://stackoverflow.com/questions/1392351/java-reflection-why-is-it-so-slow](https://stackoverflow.com/questions/1392351/java-reflection-why-is-it-so-slow)
