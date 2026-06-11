# 格式规范 - Java

---

## 1. 关键字与括号之间加空格

`if`、`for`、`while`、`switch`、`do` 等保留字与左右括号之间必须加空格，操作符两侧也需加空格：

```java
// 正确
if (xmlFile == null || !xmlFile.exists()) {
    return;
}

// 错误
if(xmlFile==null||!xmlFile.exists()){
    return;
}
```

---

## 2. 大括号格式

- 大括号内为空时：`{}`，不换行
- 非空代码块：左括号前不换行，左括号后换行，右括号前换行，右括号后如有 `else` 则不换行

```java
// 正确
public void calculateCountOfStock() {}

public void calculateCountOfStock() {
    doSomething();
}

// if-else
if (condition) {
    doA();
} else {
    doB();
}
```

---

## 3. 运算符两侧加空格

```java
// 正确
int result = a + b;
return (a + b > 0) ? "student" : "teacher";

// 错误
int result=a+b;
```

---

## 4. 方法参数逗号后加空格

```java
// 正确
invokeMethod("a", "b", "c");

// 错误
invokeMethod("a","b","c");
```

---

## 5. 方法长度不超过 50 行

过长的方法难以编写单元测试和后期维护，尽量做到**一个方法只做一件事**。

---

## 6. if 语句中避免过长的表达式

条件中不超过两个逻辑表达式，超出时提取为独立方法：

```java
// 正确
if (shouldDoSomething(a, dim)) {
    doSomething();
}

boolean shouldDoSomething(int a, Dimension dim) {
    return a > -1 && a != 1 && dim.width > 0 && dim.height > 0;
}

// 错误
if (a > -1 && a != 1 && dim.width > 0 && dim.height > 0) {
    doSomething();
}
```

---

## 7. 接口方法和属性不加修饰符

接口中的方法和属性不加 `public` 等修饰符，保持简洁：

```java
// 正确
interface Animal {
    int countOfLegs();
}

// 错误
interface Animal {
    public int countOfLegs();
}
```

---

## 8. JSON 输出格式

Java 代码中输出 JSON 时必须使用标准写法，输出数组时使用 `JSONArray`：

```java
// 错误
{a:"bb"};
{'a':"bb"};

// 正确
{"aa":"bb"};
```

```java
// 使用 JSONArray 输出数组（不要用 Java 原生数组放入 JSONObject）
JSONObject jo = JSONObject.create();
JSONArray ja = new JSONArray();
ja.put("aaa").put("bbb").put("ccc");
jo.put("names", ja);
```

---

## 9. 空字符串使用 StringUtils.EMPTY

```java
// 错误
if (name == null) {
    return "";
}

// 正确
if (name == null) {
    return StringUtils.EMPTY;
}
```

判断字符串是否为空，使用 `StringUtils.isEmpty()`：

```java
// 错误
if ("".equals(name)) { ... }

// 正确
if (StringUtils.isEmpty(name)) { ... }
```

---

## 10. 对象转字符串使用 GeneralUtils.objectToString

```java
// 错误（可能空指针）
String result = key.toString();

// 正确
String okStr = GeneralUtils.objectToString(key);
```

---

## 11. equals 和 hashCode 手写，不使用 IDE 自动生成

IDE 生成的模板在新增属性或特殊判断时难以正确处理：

```java
// 正确写法
public boolean equals(Object obj) {
    return obj instanceof MyObject
            && ComparatorUtils.equals(this.src, ((MyObject) obj).src)
            && this.isOverflowX() == ((MyObject) obj).isOverflowX()
            && this.isOverflowY() == ((MyObject) obj).isOverflowY();
}
```
