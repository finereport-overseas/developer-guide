# Java Code Standards

This section defines the Java coding standards for FanRuan plugin development. All plugins involving Java development must comply with these rules.

The standards are organized into the following categories:

| Category | Summary |
| --- | --- |
| [Format Standards](./format-standards.md) | Spacing, indentation, braces, method length, JSON output, string utilities |
| [Logging Standards](./java-logging-standards.md) | Logging method selection, format placeholders, exception naming |
| [Naming Standards](./java-naming-standards.md) | Naming conventions for classes, methods, variables, constants, and packages |
| [Output Standards](./java-output-standards.md) | Standard methods for sending data to the frontend |
| [Other Standards](./java-other-standards.md) | Constructors, collection operations, switch, access control, @Override |
| [Business Code Standards](./java-business-standards.md) | equals/hashCode, @Identifier, singletons, cross-package reference restrictions |

---

## Maximum Parameters in Public Methods

**No more than 4 parameters.** Too many parameters make a method harder to understand and use (an exception applies to HTTP handler methods that include `Request`/`Response` parameters).

## Control Flow Statements Must Use Braces

`if`, `if-else`, `for`, `while`, `do`, and `switch` statements must always be followed by braces `{}`:

```java
// Wrong
if (statement) return;

// Correct
if (statement) {
    return;
}
```

## Class Variable Ordering

Static variables before instance variables; public → protected → private:

```java
// Correct
public class Test {
    public static final String AAA = "aaa";
    public String myData = "data";
    protected String myProtectedData = "Protected";
    private String myPrivate = "Private";
}
```

## Simplify Boolean Returns

```java
// Wrong
if (booleanExpression) {
    return true;
} else {
    return false;
}

// Correct
return booleanExpression;
```

## Wrap Binary Expressions Before Ternary Operators in Parentheses

```java
// Wrong
return a + b > 0 ? "student" : "teacher";

// Correct
return (a + b > 0) ? "student" : "teacher";
```

## Do Not Override a Method with Only a super Call

```java
// Wrong (no extra logic — equivalent to not overriding at all)
@Override
public void doSomething() {
    super.doSomething();
}
```

Exception: classes implementing the `FClone` interface must override `clone()`. When this conflicts with the rule above, the interface requirement takes precedence.
