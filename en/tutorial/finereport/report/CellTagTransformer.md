# CellTagTransformer

| Property | Value |
| --- | --- |
| Interface Type | processor |
| Module | extra-report |
| Full Class Name | `com.fr.report.fun.CellTagTransformer` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/com.fr.report.fun.CellTagTransformer) |

## Overview

Modifies the HTML `Tag` emitted for a cell, useful for adding tooltip hints, custom HTML attributes, and similar purposes.

## Interface Definition

```java
/**
 * Modifies the HTML tag emitted for a cell
 */
public interface CellTagTransformer extends Mutable {

    String MARK_STRING = "CellTagTransformer";

    int CURRENT_LEVEL = 1;

    /**
     * Processes the cell tooltip effect
     *
     * @param c        Calculator
     * @param tag      Cell HTML tag
     * @param tooltip  Tooltip content
     * @param rawValue Raw cell value
     * @return Processed HTML tag
     */
    Tag process(Calculator c, Tag tag, String tooltip, Object rawValue);
}
```

## Default Implementation Reference

The built-in default implementation `DefaultCellTooltipProcessor` sets the tooltip content as the HTML `title` attribute:

```java
public class DefaultCellTooltipProcessor extends AbstractCellTagTransformer {

    public Tag process(Calculator c, Tag tag, String tooltip, Object rawValue) {
        tag.attr("title", tooltip);
        return tag;
    }
}
```

## Usage

Register your implementation class in the plugin's `plugin.xml` using the `extra-report` tag:

```xml
<extra-report>
    <CellTagTransformer class="com.fr.plugin.xxx.YourClassName"/>
</extra-report>
```

## Example Code

The following example adds a custom HTML attribute on top of the default tooltip:

```java
public class MyCellTagTransformer extends AbstractCellTagTransformer {

    @Override
    public Tag process(Calculator c, Tag tag, String tooltip, Object rawValue) {
        // Preserve the original title attribute
        tag.attr("title", tooltip);
        // Add a custom data attribute for use by front-end JS
        tag.attr("data-raw-value", rawValue == null ? "" : rawValue.toString());
        return tag;
    }
}
```

## Notes

- It is recommended to extend `AbstractCellTagTransformer` rather than implementing the interface directly, to reduce boilerplate code.
- Modifications to `tag` in `process()` directly affect the final HTML output. Be careful not to overwrite attributes already set by other plugins.
- `rawValue` is the raw cell value and may be `null`; always perform a null check before use.
