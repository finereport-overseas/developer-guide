# 其他规范 - Java

---

## 1. 构造方法禁止包含业务逻辑

初始化逻辑放在 `init()` 方法中，由构造方法调用：

```java
// 正确
public class Fish {
    public Fish() {
        init();
    }
    public void init() {
        // 初始化逻辑
    }
}

// 错误
public class Fish {
    public Fish() {
        // 直接写初始化逻辑
    }
}
```

---

## 2. 禁止向构造方法和公共方法传递 null 参数

`null` 参数很难从方法声明上理解其意义，应提供重载方法替代：

```java
// 工具方法
public class Utils {
    public static Border createBorder(Font font, Color color) {
        if (color == null) {
            color = new Color(223, 122, 123);
        }
        // ...
    }
}

// 正确：添加重载方法
public static Border createBorder(Font font) {
    return createBorder(font, null);
}
Border border = Utils.createBorder(font);

// 错误
Border border = Utils.createBorder(font, null);
```

---

## 3. 代码中的中英文字符必须做国际化

```java
// 错误
String shapeName = "圆形";

// 正确
String shapeName = Inter.getLocText("FR-Designer_Chart_Circle");
```

---

## 4. 数组返回值禁止返回 null，使用空数组替代

```java
// 正确
public String[] getNames() {
    if (a) {
        return new String[]{"张三", "李四"};
    } else {
        return new String[0];
    }
}

// 错误
public String[] getNames() {
    if (a) {
        return new String[]{"张三", "李四"};
    } else {
        return null;  // 调用方容易空指针
    }
}
```

---

## 5. switch 的 case 与 break/return 必须一一对应

防止条件贯穿导致逻辑错误：

```java
// 正确
switch (type) {
    case 1:
        someDescription = "qwe";
        break;
    case 2:
        someDescription = "xyz";
        break;
    default:
        someDescription = "mnx";
        break;
}

// 错误（case 1 会贯穿到 case 2）
switch (type) {
    case 1:
        someDescription = "qwe";
    case 2:
        someDescription = "xyz";
        break;
    ...
}
```

---

## 6. hashCode 和 equals 处理规则

1. 重写 `equals` 就必须重写 `hashCode`
2. `Set` 中存储的对象必须重写这两个方法
3. 自定义对象作为 `Map` 的键，必须重写 `hashCode` 和 `equals`

---

## 7. 集合转数组使用 toArray(T[] array)

```java
List<String> list = new ArrayList<>(2);
list.add("guan");
list.add("bao");

// 正确
String[] array = list.toArray(new String[0]);

// 错误
Object[] array = list.toArray();
```

---

## 8. 禁止在 foreach 循环中 add/remove 元素

应使用 `Iterator` 方式操作，并发场景下需对 `Iterator` 加锁。

---

## 9. 简化布尔表达式

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

---

## 10. 类成员访问控制从严

- 禁止出现 `protected` 属性（后续无法兼容处理）
- 只在当前类使用的方法/变量必须用 `private` 修饰

---

## 11. 覆写方法必须加 @Override 注解

避免父类方法签名变更后，子类方法未同步修改：

```java
// 正确
@Override
public void doSomething() {
    // 自己的实现
}
```

在插件开发过程中，帆软框架升级时接口签名可能变化，`@Override` 能及时发现不兼容问题。
