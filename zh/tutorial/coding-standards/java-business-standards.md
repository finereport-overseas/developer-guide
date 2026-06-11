# 业务代码规范 - Java

---

## 1. 对象比较使用 ComparatorUtils.equals

`Object.equals()` 容易抛空指针异常，应使用工具方法进行对象比较：

```java
// 正确
if (ComparatorUtils.equals(a, b)) {
    // do something
}

// 明确字符串比较（字符串字面量不为 null，此写法同样正确）
if ("abcde".equals(op)) {
    // do something
}

// 错误（a 可能为 null）
if (a.equals(b)) {
    // do something
}
```

> 如果需要更高性能，可以使用 `AssistUtils.equals()`。

---

## 2. 集合判空使用 isEmpty()

```java
// 正确
if (list.isEmpty()) { ... }

// 错误
if (list.size() == 0) { ... }
```

---

## 3. 配置属性必须加 @Identifier 注解

防止属性名重构时，持久化名称未做兼容导致数据丢失：

```java
// 正确
@Identifier("privateSHA256Key")
private Conf<String> privateSHA256Key = Holders.simple("aki8r-mk89t-...");

// 错误（无 @Identifier，重构属性名后持久化数据无法读取）
private Conf<String> smsAppKey = Holders.simple(StringUtils.EMPTY);
private Conf<String> smsAppSecret = Holders.simple(StringUtils.EMPTY);
```

---

## 4. 重写 equals 和 hashCode 使用 AssistUtils 模板

不使用 IDE 自动生成的代码，使用以下模板：

```java
@Override
public boolean equals(Object obj) {
    return obj instanceof ProductVersion
            && AssistUtils.equals(this.nameLocaleKey, ((ProductVersion) obj).nameLocaleKey)
            && AssistUtils.equals(this.name, ((ProductVersion) obj).name)
            && AssistUtils.equals(this.version, ((ProductVersion) obj).version)
            && AssistUtils.equals(this.level, ((ProductVersion) obj).level)
            && AssistUtils.equals(this.jarTime, ((ProductVersion) obj).jarTime);
}

@Override
public int hashCode() {
    return AssistUtils.hashCode(nameLocaleKey, name);
}
```

---

## 5. 重写 toString 使用 AssistUtils.toString

```java
@Override
public String toString() {
    return AssistUtils.toString(this);
}

// 如需排除某些属性
@Override
public String toString() {
    return AssistUtils.toString(this, "excludeProperty1", "excludeProperty2");
}
```

---

## 6. 禁止跨包引用 com.fr.report.core 下的类

`com.fr.report.core` 下的类禁止被其他包下的类直接引用：

```java
// 错误
package com.fr.report.execute;

import com.fr.report.core.BoxFactory;

public class Executor {
    public BoxFactory createBoxFactory() {
        return new BoxFactory();
    }
}
```

---

## 7. 单例推荐使用枚举模式

枚举天然线程安全且写法简洁：

```java
// 推荐
public enum StateManager {

    INSTANCE;

    private int state;

    public int getState() {
        return state;
    }

    public void setState(int state) {
        this.state = state;
    }
}
```
