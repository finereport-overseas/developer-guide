# Java 代码规范

本章节规定了帆软插件开发中 Java 代码的编写规范，所有涉及 Java 开发的插件均应遵守。

规范分为以下几个子类别：

| 类别 | 内容概要 |
| --- | --- |
| [格式规范](./format-standards.md) | 空格、缩进、大括号、方法长度、JSON 输出、字符串工具类 |
| [日志规范](./java-logging-standards.md) | 日志方法选择、格式化占位符、异常命名 |
| [命名规范](./java-naming-standards.md) | 类/方法/变量/常量/包名的命名风格与禁止事项 |
| [输出规范](./java-output-standards.md) | 向前端输出数据的标准方法 |
| [其他规范](./java-other-standards.md) | 构造方法、集合操作、switch、访问控制、@Override |
| [业务代码规范](./java-business-standards.md) | equals/hashCode、@Identifier、单例、跨包引用限制 |

---

## 公共方法参数数量

**不超过 4 个**。过多的参数不利于方法的理解和使用（HTTP 请求方法中含 `Request`/`Response` 两个参数的情形除外）。

## 控制流语句必须使用大括号

`if`、`if-else`、`for`、`while`、`do`、`switch` 语句后面必须跟大括号 `{}`：

```java
// 错误
if (statement) return;

// 正确
if (statement) {
    return;
}
```

## 类变量排列顺序

静态变量 → 实例变量，public → protected → private：

```java
// 正确
public class Test {
    public static final String AAA = "aaa";
    public String myData = "data";
    protected String myProtectedData = "Protected";
    private String myPrivate = "Private";
}
```

## 简化条件返回

```java
// 错误
if (booleanExpression) {
    return true;
} else {
    return false;
}

// 正确
return booleanExpression;
```

## 三元运算符前的二元表达式用括号括起

```java
// 错误
return a + b > 0 ? "student" : "teacher";

// 正确
return (a + b > 0) ? "student" : "teacher";
```

## 禁止仅调用 super 的空覆写方法

```java
// 错误（没有额外实现，等同于没有重写）
@Override
public void doSomething() {
    super.doSomething();
}
```

例外：实现了 `FClone` 接口的类必须重写 `clone()` 方法，与此规则冲突时以接口要求为准。
