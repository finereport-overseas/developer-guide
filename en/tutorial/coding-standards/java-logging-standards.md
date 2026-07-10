# Logging Standards - Java

---

## 1. Use the Built-in Logger — Never Call printStackTrace

```java
// Wrong
try {
    doSomething();
} catch (Exception e) {
    e.printStackTrace();
}

// Correct (FineReport / FineBI plugins)
try {
    doSomething();
} catch (Exception e) {
    FineLoggerFactory.getLogger().error(e.getMessage(), e);
}
```

---

## 2. Use Format Placeholders for INFO / DEBUG Logs — Do Not Concatenate Strings

```java
// Correct
FineLoggerFactory.getLogger().info("Hello, I am {}.", "test");
// Output: Hello, I am test.

FineLoggerFactory.getLogger().debug("Hello, I am {}, it is a {}.", "test", "sheep");
// Output: Hello, I am test, it is a sheep.
```

String concatenation is evaluated even when the log level is disabled, wasting CPU. Format-style methods only build the string when the log level is active.

---

## 3. Name Intentionally Ignored Exceptions ignore

```java
// Correct
try {
    doSomething();
} catch (Exception ignore) {
    // Intentionally ignoring this exception
}
```

Naming the variable `ignore` makes it clear that the exception is deliberately not handled, preventing others from mistaking it for an overlooked error.
