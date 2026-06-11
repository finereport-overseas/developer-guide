# 命名规范 - Java

命名要能大致体现出方法/类的功能，不能使用单个无意义的单词。

---

## 1. 基础命名风格

| 类型 | 风格 | 示例 |
| --- | --- | --- |
| 类名 | UpperCamelCase | `UserDAO`、`NetworkHelper` |
| 方法名 | lowerCamelCase | `getPureMessage()`、`calculateCount()` |
| 变量名 | lowerCamelCase | `localValue`、`userTextField` |
| 常量 | ALL_CAPS_WITH_UNDERSCORES | `MAX_STOCK_COUNT` |
| 包名 | 全小写，单数形式 | `com.fr.data.model` |

特殊类名例外：`URL`、`HTML` 等缩写词保持大写。

```java
// 正确
public class DateWork {
    private int remainDays = 10;  // 剩余天数

    public void calculateNameWithData(Data data) {}
}

// 错误（使用了无意义缩写）
public class DateWork {
    private int r = 10;

    public void cal(Data data) {}
}
```

---

## 2. 命名禁止事项

- **不能**以下划线 `_` 或美元符号 `$` 开头或结尾
- **禁止**拼音与英文混合，更不允许直接使用中文
- **禁止**完全不规范的缩写（望文不知义）

```java
// 错误
public static final int max_stock_count = 20;  // 常量未全大写
public static final int MAX_COUNT = 20;         // 语义不完整

// 正确
public static final int MAX_STOCK_COUNT = 20;
```

---

## 3. 类命名特殊规则

- 抽象类：以 `Abstract` 开头
- 异常类：以 `Exception` 结尾
- 测试类：以被测类名开始，以 `Test` 结尾
- 领域模型：DO / BO / DTO / VO 等后缀保持大写

```java
// 正确
UserDAO, ParameterNameSpace, NetworkHelper

// 错误
userDAO, ParameternameSpace, NETworkHelper
```

---

## 4. 包名规范

包名使用小写，点分隔符之间有且仅有一个语义单词，使用单数形式：

```java
// 正确
package com.fr.data.model;

// 错误
package com.fr.chart.ChartAttr;       // 含大写字母
package com.finebi.common.internalimp; // 多个语义单词混合
```

---

## 5. set / get / is / has 方法前缀

- 设置属性：`setXxx()`
- 获取布尔属性：`isXxx()` 或 `hasXxx()`
- 获取其他属性：`getXxx()`

```java
// 正确
public class Student {
    private boolean male;

    public boolean isMale() {
        return male;
    }
}

// 错误
public boolean getMale() { ... }
```

布尔类型的**字段名**不能以 `is`/`has` 开头（会导致部分框架序列化出错）：

```java
// 错误
private boolean isMale;

// 正确
private boolean male;
public boolean isMale() { return male; }
```

---

## 6. 禁止魔术数，使用常量替代

```java
// 错误
public double area(double r) {
    return 3.14 * r * r;
}

// 正确
private static final double PI = 3.14;
public double area(double r) {
    return PI * r * r;
}
```

---

## 7. 开放接口命名不使用 "Custom"

作为开放接口本身已代表"可自定义实现"，无需再强调：

```java
// 正确
public interface PasswordHandleProcessor extends XMLable, Immutable {
    String encrypt(String clientPassword);
    String decrypt(String clientPassword);
}

// 错误
public interface CustomPasswordCryptProcessor extends XMLable, Immutable {
    String encodePassword(String clientPassword);
    String decodePassword(String clientPassword);
}
```

---

## 8. 返回自身的链式方法不以 set 开头

需要支持链式调用时，提供两个方法：一个标准 setter，一个返回 `this` 的方法：

```java
// 正确
public void setIp(String ip) {
    this.ip = ip;
}

public BlockIpEntity ip(String ip) {
    this.setIp(ip);
    return this;
}

// 错误
public BlockIpEntity setIp(String ip) {
    this.ip = ip;
    return this;
}
```
