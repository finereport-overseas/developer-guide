# StringTypeAttrProvider

| Property | Value |
| --- | --- |
| Interface Type | provider |
| Module | extra-report |
| Full Class Name | `com.fr.report.fun.StringTypeAttrProvider` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/com.fr.report.fun.StringTypeAttrProvider) |

## Overview

Performs dynamic computation transformations on string-type attribute values in report business logic, commonly used to support formula evaluation. Currently, only the password attributes for Excel and PDF export support modification via this interface.

## Interface Definition

```java
/**
 * String parsing interface
 *
 * @version 10.0
 */
public interface StringTypeAttrProvider extends Selectable {

    String MARK_STRING = "StringTypeAttrProvider";

    int CURRENT_LEVEL = 1;

    /**
     * Transforms a string attribute value (e.g. evaluates a formula expression)
     *
     * @param c    Calculator
     * @param attr Original attribute value
     * @return Processed string
     */
    String process(CalculatorProvider c, String attr);

    /**
     * Attribute key enum, identifies which attribute is being processed
     */
    enum AttrKey {
        /** Export password attribute */
        Password_Export,
        /** Default */
        Default
    }
}
```

## Usage

Register your implementation class in the plugin's `plugin.xml` using the `extra-report` tag:

```xml
<extra-report>
    <StringTypeAttrProvider class="com.fr.plugin.xxx.YourClassName"/>
</extra-report>
```

## Example Code

The following example dynamically evaluates the export password attribute value as a formula:

```java
public class MyStringTypeAttrProvider implements StringTypeAttrProvider {

    @Override
    public Selector selector() {
        return new Selector() {
            @Override
            public boolean accept(ObjectHolder holder) {
                // Only process the export password attribute
                return holder != null
                    && AttrKey.Password_Export.equals(holder.get(AttrKey.class));
            }
        };
    }

    @Override
    public String process(CalculatorProvider c, String attr) {
        // Evaluate the attribute value as a formula expression, e.g. attr = "${username}_pass"
        try {
            Object result = c.eval(attr);
            return result == null ? attr : result.toString();
        } catch (Exception e) {
            return attr;
        }
    }
}
```

## Notes

- Currently, only `AttrKey.Password_Export` (export password) has been fully integrated. Other attribute keys (`Default`, etc.) are not yet effective.
- The `attr` parameter in `process()` is the raw configuration string, which may be a plain string or a formula expression. Implementations must determine whether evaluation is needed.
- This interface extends `Selectable`; use the `accept()` method of `selector()` to control the scope of application.
