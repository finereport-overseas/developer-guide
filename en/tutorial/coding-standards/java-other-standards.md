# Other Standards - Java

---

## 1. No Business Logic in Constructors

Put initialization logic in an `init()` method and call it from the constructor:

```java
// Correct
public class Fish {
    public Fish() {
        init();
    }
    public void init() {
        // initialization logic
    }
}

// Wrong
public class Fish {
    public Fish() {
        // business logic written directly here
    }
}
```

---

## 2. Do Not Pass null to Constructors or Public Methods

`null` arguments are hard to understand from a method signature alone. Provide overloaded methods instead:

```java
// Utility method
public class Utils {
    public static Border createBorder(Font font, Color color) {
        if (color == null) {
            color = new Color(223, 122, 123);
        }
        // ...
    }
}

// Correct: add an overload
public static Border createBorder(Font font) {
    return createBorder(font, null);
}
Border border = Utils.createBorder(font);

// Wrong
Border border = Utils.createBorder(font, null);
```

---

## 3. Internationalize All User-Visible Text

```java
// Wrong
String shapeName = "Circle";

// Correct
String shapeName = Inter.getLocText("FR-Designer_Chart_Circle");
```

---

## 4. Never Return null from Array-Returning Methods — Use an Empty Array

```java
// Correct
public String[] getNames() {
    if (a) {
        return new String[]{"Alice", "Bob"};
    } else {
        return new String[0];
    }
}

// Wrong
public String[] getNames() {
    if (a) {
        return new String[]{"Alice", "Bob"};
    } else {
        return null;  // callers risk a NullPointerException
    }
}
```

---

## 5. Every switch case Must Have a Corresponding break or return

Prevent fall-through from causing logic errors:

```java
// Correct
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

// Wrong (case 1 falls through into case 2)
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

## 6. hashCode and equals Rules

1. If you override `equals`, you must also override `hashCode`.
2. Objects stored in a `Set` must override both methods.
3. Custom objects used as `Map` keys must override both `hashCode` and `equals`.

---

## 7. Use toArray(T[] array) When Converting a Collection to an Array

```java
List<String> list = new ArrayList<>(2);
list.add("guan");
list.add("bao");

// Correct
String[] array = list.toArray(new String[0]);

// Wrong
Object[] array = list.toArray();
```

---

## 8. Do Not add or remove Elements Inside a foreach Loop

Use an `Iterator` instead. In concurrent scenarios, synchronize on the `Iterator`.

---

## 9. Simplify Boolean Returns

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

---

## 10. Apply Strict Access Control to Class Members

- `protected` fields are prohibited (they cannot be handled compatibly in the future).
- Methods and variables used only within the current class must be declared `private`.

---

## 11. Always Add @Override When Overriding a Method

This catches incompatibilities when a parent class method signature changes:

```java
// Correct
@Override
public void doSomething() {
    // custom implementation
}
```

During plugin development, FanRuan framework upgrades may change interface signatures. `@Override` ensures such breaking changes are caught at compile time.
