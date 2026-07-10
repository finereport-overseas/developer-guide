# Business Code Standards - Java

---

## 1. Use ComparatorUtils.equals for Object Comparison

`Object.equals()` can throw a NullPointerException. Use the utility method instead:

```java
// Correct
if (ComparatorUtils.equals(a, b)) {
    // do something
}

// Explicit string comparison (string literals are never null, so this is also acceptable)
if ("abcde".equals(op)) {
    // do something
}

// Wrong (a may be null)
if (a.equals(b)) {
    // do something
}
```

> For higher performance, `AssistUtils.equals()` can be used instead.

---

## 2. Use isEmpty() to Check Collections

```java
// Correct
if (list.isEmpty()) { ... }

// Wrong
if (list.size() == 0) { ... }
```

---

## 3. Configuration Properties Must Be Annotated with @Identifier

This prevents data loss when a property is renamed during refactoring, since the persisted name is preserved:

```java
// Correct
@Identifier("privateSHA256Key")
private Conf<String> privateSHA256Key = Holders.simple("aki8r-mk89t-...");

// Wrong (no @Identifier — persisted data cannot be read if the field is renamed)
private Conf<String> smsAppKey = Holders.simple(StringUtils.EMPTY);
private Conf<String> smsAppSecret = Holders.simple(StringUtils.EMPTY);
```

---

## 4. Use the AssistUtils Template for equals and hashCode

Do not use IDE-generated code. Use the following template:

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

## 5. Use AssistUtils.toString to Override toString

```java
@Override
public String toString() {
    return AssistUtils.toString(this);
}

// To exclude specific properties
@Override
public String toString() {
    return AssistUtils.toString(this, "excludeProperty1", "excludeProperty2");
}
```

---

## 6. Do Not Reference Classes Under com.fr.report.core from Other Packages

Classes under `com.fr.report.core` must not be directly referenced from classes in other packages:

```java
// Wrong
package com.fr.report.execute;

import com.fr.report.core.BoxFactory;

public class Executor {
    public BoxFactory createBoxFactory() {
        return new BoxFactory();
    }
}
```

---

## 7. Prefer the Enum Pattern for Singletons

Enums are inherently thread-safe and concise:

```java
// Recommended
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
