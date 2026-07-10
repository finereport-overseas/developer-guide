# ErrorMessageProcessor

| Property | Value |
| --- | --- |
| Interface Type | processor |
| Module | extra-report |
| Full Class Name | `com.fr.report.fun.ErrorMessageProcessor` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/com.fr.report.fun.ErrorMessageProcessor) |

## Overview

> **Deprecated (`@Deprecated`):** This interface has been marked as deprecated. Use [`StringTypeAttrProvider`](../report/StringTypeAttrProvider.md) instead.

Applies custom processing to error message strings in reports, such as replacing or translating error prompt content.

## Interface Definition

```java
/**
 * String parsing interface; use StringTypeAttrProvider instead
 * @deprecated
 */
@Deprecated
public interface ErrorMessageProcessor extends Immutable {

    int CURRENT_LEVEL = 1;

    String MARK_STRING = "ErrorMessageProcessor";

    /**
     * Processes an error message
     *
     * @param c            Calculator
     * @param errorMessage Original error message
     * @return Processed error message
     */
    String process(Calculator c, String errorMessage);
}
```

## Usage

Register your implementation class in the plugin's `plugin.xml` using the `extra-report` tag:

```xml
<extra-report>
    <ErrorMessageProcessor class="com.fr.plugin.xxx.YourClassName"/>
</extra-report>
```

## Example Code

```java
public class MyErrorMessageProcessor implements ErrorMessageProcessor {

    @Override
    public String process(Calculator c, String errorMessage) {
        // Replace an English error message with a localized one
        if (errorMessage != null && errorMessage.contains("OutOfMemory")) {
            return "Insufficient memory, please contact the administrator";
        }
        return errorMessage;
    }
}
```

## Notes

- This interface is marked `@Deprecated`. Use `StringTypeAttrProvider` for new projects.
- This interface extends `Immutable` and cannot be hot-replaced at runtime.
