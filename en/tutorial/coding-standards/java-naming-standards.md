# Naming Standards - Java

Names should broadly reflect what a method or class does. Single meaningless words are not acceptable.

---

## 1. Basic Naming Conventions

| Type | Style | Examples |
| --- | --- | --- |
| Class name | UpperCamelCase | `UserDAO`, `NetworkHelper` |
| Method name | lowerCamelCase | `getPureMessage()`, `calculateCount()` |
| Variable name | lowerCamelCase | `localValue`, `userTextField` |
| Constant | ALL_CAPS_WITH_UNDERSCORES | `MAX_STOCK_COUNT` |
| Package name | all lowercase, singular | `com.fr.data.model` |

Exception: acronyms like `URL` and `HTML` remain fully uppercase in class names.

```java
// Correct
public class DateWork {
    private int remainDays = 10;  // remaining days

    public void calculateNameWithData(Data data) {}
}

// Wrong (meaningless abbreviations)
public class DateWork {
    private int r = 10;

    public void cal(Data data) {}
}
```

---

## 2. Naming Prohibitions

- Names **must not** start or end with an underscore `_` or dollar sign `$`.
- **Do not** mix pinyin with English, and **never** use Chinese characters directly.
- **Do not** use completely non-standard abbreviations that are not self-explanatory.

```java
// Wrong
public static final int max_stock_count = 20;  // constant not in ALL_CAPS
public static final int MAX_COUNT = 20;         // incomplete semantics

// Correct
public static final int MAX_STOCK_COUNT = 20;
```

---

## 3. Special Naming Rules for Classes

- Abstract classes: prefix with `Abstract`
- Exception classes: suffix with `Exception`
- Test classes: start with the name of the class under test, suffix with `Test`
- Domain models: suffixes such as DO, BO, DTO, VO remain uppercase

```java
// Correct
UserDAO, ParameterNameSpace, NetworkHelper

// Wrong
userDAO, ParameternameSpace, NETworkHelper
```

---

## 4. Package Naming

Package names are all lowercase, with a single semantic word between each dot separator, in singular form:

```java
// Correct
package com.fr.data.model;

// Wrong
package com.fr.chart.ChartAttr;       // contains uppercase letters
package com.finebi.common.internalimp; // multiple semantic words combined
```

---

## 5. Prefixes for set / get / is / has Methods

- Setting a property: `setXxx()`
- Getting a boolean property: `isXxx()` or `hasXxx()`
- Getting other properties: `getXxx()`

```java
// Correct
public class Student {
    private boolean male;

    public boolean isMale() {
        return male;
    }
}

// Wrong
public boolean getMale() { ... }
```

Boolean **field names** must not start with `is` or `has` (this can cause serialization errors in some frameworks):

```java
// Wrong
private boolean isMale;

// Correct
private boolean male;
public boolean isMale() { return male; }
```

---

## 6. No Magic Numbers — Use Named Constants

```java
// Wrong
public double area(double r) {
    return 3.14 * r * r;
}

// Correct
private static final double PI = 3.14;
public double area(double r) {
    return PI * r * r;
}
```

---

## 7. Do Not Use "Custom" in Public Interface Names

A public interface already implies "customizable implementation" — there is no need to emphasize it:

```java
// Correct
public interface PasswordHandleProcessor extends XMLable, Immutable {
    String encrypt(String clientPassword);
    String decrypt(String clientPassword);
}

// Wrong
public interface CustomPasswordCryptProcessor extends XMLable, Immutable {
    String encodePassword(String clientPassword);
    String decodePassword(String clientPassword);
}
```

---

## 8. Fluent Builder Methods That Return this Must Not Start with set

Provide two separate methods: a standard setter and a fluent method that returns `this`:

```java
// Correct
public void setIp(String ip) {
    this.ip = ip;
}

public BlockIpEntity ip(String ip) {
    this.setIp(ip);
    return this;
}

// Wrong
public BlockIpEntity setIp(String ip) {
    this.ip = ip;
    return this;
}
```
