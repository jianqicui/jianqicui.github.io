---
layout: post
title: Java 动态性之脚本引擎
category: Java
tags: [JVM, 动态性, 脚本引擎]
keywords: JVM,动态性,脚本引擎
excerpt: Java 脚本引擎使得应用程序可以通过一套固定的接口与各种脚本引擎交互，从而达到在 Java 平台上调用各种脚本语言的目的。Java 脚本 API 是连通 Java 平台和脚本语言的桥梁，可以把一些复杂异变的业务逻辑交给脚本语言处理，这又大大提高了开发效率。
---

Java 脚本引擎使得应用程序可以通过一套固定的接口与各种脚本引擎交互，从而达到在 Java 平台上调用各种脚本语言的目的。Java 脚本 API 是连通 Java 平台和脚本语言的桥梁，可以把一些复杂异变的业务逻辑交给脚本语言处理，这又大大提高了开发效率。

## JavaScript 引擎

自从 Java1.6 以后 Java 就自带 JavaScript 引擎，这个支持来自于 JSR223 规范，对应的包是 javax.script。1.6、1.7 使用 Rhino 引擎，1.8 使用 Nashorn 引擎。
Rhino 和 Nashorn 都是用 Java 实现的 JavaScript 引擎。它们自身都是普通的 Java 程序，运行在 JVM 上。

## 代码示例

```
import java.io.File;
import java.nio.charset.StandardCharsets;
import java.util.List;

import javax.script.Invocable;
import javax.script.ScriptEngine;
import javax.script.ScriptEngineManager;

import org.apache.commons.io.FileUtils;
import org.junit.Test;

public class JavaScriptEngine {

    @SuppressWarnings("unchecked")
    @Test
    public void testJavaScript() throws Exception {
        // 获取脚本引擎对象
        ScriptEngineManager scriptEngineManager = new ScriptEngineManager();
        ScriptEngine scriptEngine = scriptEngineManager.getEngineByName("javascript");

        // 执行脚本
        String str = "var user = {name: '张三',  age: 18}; print(user.name);";
        scriptEngine.eval(str);

        // 定义变量
        scriptEngine.put("msg", "Hello World");
        System.out.println(scriptEngine.get("msg"));
        scriptEngine.eval("msg='Hello Java'");
        System.out.println(scriptEngine.get("msg"));

        // 定义函数
        scriptEngine.eval("function add(a, b) { return a + b; }");
        // 取得调用接口
        Invocable invocable = (Invocable) scriptEngine;
        // 执行函数中的方法
        Object result = invocable.invokeFunction("add", 11, 12);
        System.out.println(result);

        // 导入java包，使用其他包中的java类
        String jsCode = "var list = java.util.Arrays.asList([\"Java\", \"Html\", \"MySQL\"])";
        scriptEngine.eval(jsCode);
        List<String> list = (List<String>) scriptEngine.get("list");
        for (String string : list) {
            System.out.println(string);
        }

        File file = new File("/tmp/test.js");
        final String jsContent = FileUtils.readFileToString(file, StandardCharsets.UTF_8);
        scriptEngine.eval(jsContent);
    }
}
```

**test.js**
```
function test(){
    var a = 1;
    var b = 2;

    print("invoke js file: " + (a + b));   
}

test();
```

**输出**
```
Hello World
Hello Java
23.0
Java
Html
MySQL
invoke js file: 3
```

## 参考

[https://segmentfault.com/a/1190000016850583](https://segmentfault.com/a/1190000016850583)

[http://xxk.b3log.org/articles/2020/02/20/1582193878553](http://xxk.b3log.org/articles/2020/02/20/1582193878553)

[https://www.cnblogs.com/xzlf/p/12683473.html](https://www.cnblogs.com/xzlf/p/12683473.html)
