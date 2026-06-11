# 日志规范 - Java

---

## 1. 使用内置日志方法，禁止 printStackTrace

```java
// 错误
try {
    doSomething();
} catch (Exception e) {
    e.printStackTrace();
}

// 正确（报表/BI 插件）
try {
    doSomething();
} catch (Exception e) {
    FineLoggerFactory.getLogger().error(e.getMessage(), e);
}
```

---

## 2. INFO / DEBUG 日志使用格式化占位符，不拼接字符串

```java
// 正确
FineLoggerFactory.getLogger().info("Hello, I am {}.", "test");
// 输出：Hello, I am test.

FineLoggerFactory.getLogger().debug("Hello, I am {}, it is a {}.", "test", "sheep");
// 输出：Hello, I am test, it is a sheep.
```

字符串拼接在日志级别未开启时仍会执行，浪费性能；格式化方法只有在日志开启时才会拼接。

---

## 3. 忽略异常时，将异常变量命名为 ignore

```java
// 正确
try {
    doSomething();
} catch (Exception ignore) {
    // 有意忽略该异常
}
```

命名为 `ignore` 能明确表达"此处故意不处理异常"，避免他人误以为是遗漏了处理逻辑。
