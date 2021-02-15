---
layout: post
title: JMX 中 MBean 和 MXBean 的区别
category: Java
tags: [JMX, MBean, MXBean]
keywords: JMX,MBean,MXBean
excerpt: MBean 表示被管理的资源，MXBean 其实也是被管理的资源，但 MBean 的属性中不能包含自定义对象，而 MXBean 可以。
---

MBean 表示被管理的资源，MXBean 其实也是被管理的资源，但 MBean 的属性中不能包含自定义对象，而 MXBean 可以。

## MBean

### 定义 MBean 接口

必须以 MBean 结尾。

```
public interface HelloMBean {

    String getName();

    void setName(String name);

    String getAge();

    void setAge(String age);

    User getUser();

    void setUser(User user);
}
```

### 定义 MBean 实现类

```
public class Hello implements HelloMBean {

    private String name;

    private String age;

    private User user;

    public User getUser() {
        System.out.println("get user 123");
        return user;
    }

    public void setUser(User user) {
        System.out.println("set user 123");
        this.user = user;
    }

    public String getName() {
        System.out.println("get name 123");
        return name;
    }

    public void setName(String name) {
        System.out.println("set name 123");
        this.name = name;
    }

    public String getAge() {
        System.out.println("get age 123");
        return age;
    }

    public void setAge(String age) {
        System.out.println("set age 123");
        this.age = age;
    }

    // 自定义对象
    public static class User {

        private String username;
        private String password;

        public User() {

        }

        public User(String username, String password) {
            this.username = username;
            this.password = password;
        }

        public void setPassword(String password) {
            this.password = password;
        }

        public void setUsername(String username) {
            this.username = username;
        }

        public String getPassword() {
            return password;
        }

        public String getUsername() {
            return username;
        }
    }
}
```

### 定义 MXBean 适配层

```
public class HelloAgent {

    public static void main(String[] args) throws Exception {
        MBeanServer server = ManagementFactory.getPlatformMBeanServer();
        // 规范 域名:name=MBean名称
        ObjectName helloName = new ObjectName("jmxBean:name=hello");
        // 注册MBean
        Hello hello = new Hello();
        hello.setAge("12");
        hello.setName("lisi");
        hello.setUser(new User("lisi", "123"));
        server.registerMBean(hello, helloName);
        Thread.sleep(60 * 60 * 1000);
    }
}
```

### 使用 JConsole 连接 MXBean

可以看到 MBean 中的 User 对象是不可用的。

![](/assets/images/2021/JConsole_MBean.png)

## MXBean

### 定义 MXBean 接口

不能以 MBean 结尾，类上有 `@MXBean` 注解

```
@MXBean
public interface HelloBean {

    String getName();

    void setName(String name);

    String getAge();

    void setAge(String age);

    User getUser();

    void setUser(User user);
}
```

### 定义 MXBean 实现类

```
public class Hello implements HelloBean {

    private String name;

    private String age;

    private User user;

    public User getUser() {
        System.out.println("get user 123");
        return user;
    }

    public void setUser(User user) {
        System.out.println("set user 123");
        this.user = user;
    }

    public String getName() {
        System.out.println("get name 123");
        return name;
    }

    public void setName(String name) {
        System.out.println("set name 123");
        this.name = name;
    }

    public String getAge() {
        System.out.println("get age 123");
        return age;
    }

    public void setAge(String age) {
        System.out.println("set age 123");
        this.age = age;
    }

    // 自定义对象
    public static class User {

        private String username;
        private String password;

        public User() {

        }

        public User(String username, String password) {
            this.username = username;
            this.password = password;
        }

        public void setPassword(String password) {
            this.password = password;
        }

        public void setUsername(String username) {
            this.username = username;
        }

        public String getPassword() {
            return password;
        }

        public String getUsername() {
            return username;
        }
    }
}
```

### 定义 MXBean 适配层

```
public class HelloAgent {

    public static void main(String[] args) throws Exception {
        MBeanServer server = ManagementFactory.getPlatformMBeanServer();
        // 规范 域名:name=MBean名称
        ObjectName helloName = new ObjectName("jmxBean:name=hello");
        // 注册MBean
        Hello hello = new Hello();
        hello.setAge("12");
        hello.setName("lisi");
        hello.setUser(new User("lisi", "123"));
        server.registerMBean(hello, helloName);
        Thread.sleep(60 * 60 * 1000);
    }
}
```

### 使用 JConsole 连接 MXBean

可以看到 MXBean 中的 User 属性包装成了 CompositeDataSupport 对象。

![](/assets/images/2021/JConsole_MXBean.png)

## 源码解析

```
class MXBeanIntrospector extends MBeanIntrospector<ConvertingMethod> {
    @Override
    MBeanAttributeInfo getMBeanAttributeInfo(String attributeName, ConvertingMethod getter, ConvertingMethod setter) {

        final boolean isReadable = (getter != null);
        final boolean isWritable = (setter != null);
        final boolean isIs = isReadable && getName(getter).startsWith("is");

        final String description = attributeName;

        final OpenType<?> openType;
        final Type originalType;
        if (isReadable) {
            openType = getter.getOpenReturnType();
            originalType = getter.getGenericReturnType();
        } else {
            openType = setter.getOpenParameterTypes()[0];
            originalType = setter.getGenericParameterTypes()[0];
        }
        Descriptor descriptor = typeDescriptor(openType, originalType);
        if (isReadable) {
            descriptor = ImmutableDescriptor.union(descriptor, getter.getDescriptor());
        }
        if (isWritable) {
            descriptor = ImmutableDescriptor.union(descriptor, setter.getDescriptor());
        }

        final MBeanAttributeInfo ai;
        if (canUseOpenInfo(originalType)) {
            ai = new OpenMBeanAttributeInfoSupport(attributeName, description, openType, isReadable, isWritable, isIs,
                    descriptor);
        } else {
            ai = new MBeanAttributeInfo(attributeName, originalTypeString(originalType), description, isReadable,
                    isWritable, isIs, descriptor);
        }
        // could also consult annotations for defaultValue,
        // minValue, maxValue, legalValues

        return ai;
    }

    private static boolean canUseOpenInfo(Type type) {
        if (type instanceof GenericArrayType) {
            return canUseOpenInfo(((GenericArrayType) type).getGenericComponentType());
        } else if (type instanceof Class<?> && ((Class<?>) type).isArray()) {
            return canUseOpenInfo(((Class<?>) type).getComponentType());
        }
        return (!(type instanceof Class<?> && ((Class<?>) type).isPrimitive()));
    }
}
```

MBean 中的属性为 MBeanAttributeInfo 对象，而 MXBean 中的自定义对象属性为 OpenMBeanAttributeInfoSupport 对象。

```
private final class CompositeMapping extends NonNullMXBeanMapping {

    @Override
    final Object toNonNullOpenValue(Object value) throws OpenDataException {
        CompositeType ct = (CompositeType) getOpenType();
        if (value instanceof CompositeDataView)
            return ((CompositeDataView) value).toCompositeData(ct);
        if (value == null)
            return null;

        Object[] values = new Object[getters.length];
        for (int i = 0; i < getters.length; i++) {
            try {
                Object got = MethodUtil.invoke(getters[i], value, (Object[]) null);
                values[i] = getterMappings[i].toOpenValue(got);
            } catch (Exception e) {
                throw openDataException("Error calling getter for " + itemNames[i] + ": " + e, e);
            }
        }
        return new CompositeDataSupport(ct, itemNames, values);
    }
}
```

当 JConsole 访问 User 对象时，将 User 对象转换成 CompositeDataSupport 对象。

[https://www.cnblogs.com/strongmore/p/14053569.html](https://www.cnblogs.com/strongmore/p/14053569.html)

[https://www.cnblogs.com/strongmore/p/14056758.html](https://www.cnblogs.com/strongmore/p/14056758.html)
