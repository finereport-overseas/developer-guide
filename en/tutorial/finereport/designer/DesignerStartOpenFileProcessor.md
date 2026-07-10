# DesignerStartOpenFileProcessor

| Property | Value |
| --- | --- |
| Interface Type | processor |
| Module | extra-designer |
| Full Class Name | `com.fr.design.fun.DesignerStartOpenFileProcessor` |
| Official Docs | [View Documentation](https://wiki.fanruan.com/display/PD/com.fr.design.fun.DesignerStartOpenFileProcessor) |

## Overview

Specifies the default template file displayed when the designer starts. This can be used to customize the designer's initial screen — for example, automatically opening a blank template or a specific template on startup.

## Interface Definition

```java
/**
 * Specifies the file to open by default when the designer starts.
 */
public interface DesignerStartOpenFileProcessor extends Immutable {

    int CURRENT_LEVEL = 1;

    String XML_TAG = "DesignerStartOpenFileProcessor";

    /**
     * Returns the report file to open when the designer starts.
     */
    FILE fileToShow();
}
```

## Usage

Register the implementation class in the plugin's `plugin.xml` via the `extra-designer` tag:

```xml
<extra-designer>
    <DesignerStartOpenFileProcessor class="com.fr.plugin.xxx.YourClassName"/>
</extra-designer>
```

## Example Code

The following example opens a blank template on startup:

```java
public class DesignerStartWithEmptyFile implements DesignerStartOpenFileProcessor {

    @Override
    public FILE fileToShow() {
        // Returns a blank template file (path relative to the template root directory)
        return new MemFILE("__empty_template__.cpt");
    }
}
```

## Reference

Official example source: [plugin-performance / dzstartemptyfile](http://cloud.finedevelop.com:2015/projects/PG/repos/plugin-performance/browse/src/com/fr/plugin/performance/dzstartemptyfile/)

## Notes

- When `fileToShow()` returns `null`, the designer uses its default startup behavior.
- This interface extends `Immutable` and does not support runtime hot-swapping via the `Mutable` mechanism.
- If multiple implementations are registered simultaneously, only the first one takes effect.
