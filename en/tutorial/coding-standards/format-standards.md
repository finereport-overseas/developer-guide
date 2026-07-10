# Format Standards - Java

---

## 1. Add Spaces Around Keywords and Parentheses

Reserved words such as `if`, `for`, `while`, `switch`, and `do` must have a space before and after parentheses. Operators must also have spaces on both sides:

```java
// Correct
if (xmlFile == null || !xmlFile.exists()) {
    return;
}

// Wrong
if(xmlFile==null||!xmlFile.exists()){
    return;
}
```

---

## 2. Brace Style

- Empty block: `{}` on one line, no line break.
- Non-empty block: opening brace on the same line (no line break before it), line break after opening brace, line break before closing brace, no line break after closing brace if followed by `else`.

```java
// Correct
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

## 3. Spaces Around Operators

```java
// Correct
int result = a + b;
return (a + b > 0) ? "student" : "teacher";

// Wrong
int result=a+b;
```

---

## 4. Space After Comma in Method Arguments

```java
// Correct
invokeMethod("a", "b", "c");

// Wrong
invokeMethod("a","b","c");
```

---

## 5. Method Length Must Not Exceed 50 Lines

Long methods are hard to unit test and maintain. Aim for **one method, one responsibility**.

---

## 6. Avoid Complex Expressions in if Conditions

No more than two logical expressions in a condition. Extract longer conditions into a dedicated method:

```java
// Correct
if (shouldDoSomething(a, dim)) {
    doSomething();
}

boolean shouldDoSomething(int a, Dimension dim) {
    return a > -1 && a != 1 && dim.width > 0 && dim.height > 0;
}

// Wrong
if (a > -1 && a != 1 && dim.width > 0 && dim.height > 0) {
    doSomething();
}
```

---

## 7. No Access Modifiers on Interface Methods and Fields

Interface methods and fields should not have `public` or other access modifiers — keep them concise:

```java
// Correct
interface Animal {
    int countOfLegs();
}

// Wrong
interface Animal {
    public int countOfLegs();
}
```

---

## 8. JSON Output Format

When outputting JSON from Java code, always use the standard approach. Use `JSONArray` for arrays:

```java
// Wrong
{a:"bb"};
{'a':"bb"};

// Correct
{"aa":"bb"};
```

```java
// Use JSONArray for arrays (do not put a Java native array into JSONObject)
JSONObject jo = JSONObject.create();
JSONArray ja = new JSONArray();
ja.put("aaa").put("bbb").put("ccc");
jo.put("names", ja);
```

---

## 9. Use StringUtils.EMPTY for Empty Strings

```java
// Wrong
if (name == null) {
    return "";
}

// Correct
if (name == null) {
    return StringUtils.EMPTY;
}
```

Use `StringUtils.isEmpty()` to check whether a string is empty:

```java
// Wrong
if ("".equals(name)) { ... }

// Correct
if (StringUtils.isEmpty(name)) { ... }
```

---

## 10. Use GeneralUtils.objectToString to Convert Objects to Strings

```java
// Wrong (may throw NullPointerException)
String result = key.toString();

// Correct
String okStr = GeneralUtils.objectToString(key);
```

---

## 11. Write equals and hashCode Manually — Do Not Use IDE Auto-Generation

IDE-generated templates are difficult to maintain correctly when new fields are added or special comparisons are required:

```java
// Correct approach
public boolean equals(Object obj) {
    return obj instanceof MyObject
            && ComparatorUtils.equals(this.src, ((MyObject) obj).src)
            && this.isOverflowX() == ((MyObject) obj).isOverflowX()
            && this.isOverflowY() == ((MyObject) obj).isOverflowY();
}
```
